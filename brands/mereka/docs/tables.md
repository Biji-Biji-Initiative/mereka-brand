# Tables

> Tables display data in a structured grid format. They are essential for admin dashboards, showing lists of users, bookings, experiences, and transactions with sortable columns, pagination, and row actions.

**Usage Context**: Admin dashboards, data management pages, reports

---

## Table Architecture

```
Table Container
├── Table Header
│   ├── Select All Checkbox (optional)
│   ├── Column Headers
│   │   ├── Column Title
│   │   └── Sort Indicator (optional)
│   └── Actions Column Header
├── Table Body
│   ├── Table Rows
│   │   ├── Row Checkbox (optional)
│   │   ├── Data Cells
│   │   └── Row Actions
│   └── Empty State / Loading State
└── Table Footer
    ├── Selection Info
    ├── Pagination Info
    └── Pagination Controls
```

---

## Quick Reference

| Feature | Description | States |
|---------|-------------|--------|
| **Basic Table** | Simple data display | Default |
| **Sortable Columns** | Click to sort | Unsorted, Ascending, Descending |
| **Pagination** | Navigate pages | First, Previous, Page Numbers, Next, Last |
| **Row Selection** | Checkbox selection | Unchecked, Checked, Indeterminate |
| **Row Actions** | Edit, delete, view | Default, Hover, Disabled |
| **Empty State** | No data display | Empty, Filtered Empty |
| **Loading State** | Skeleton rows | Loading |
| **Responsive** | Mobile adaptation | Table (desktop), Cards (mobile) |

---

## 1. Basic Table Structure

The foundation for all data tables.

### Basic Table Implementation

```tsx
interface Column<T> {
  key: keyof T | string
  header: string
  width?: string
  align?: 'left' | 'center' | 'right'
  render?: (value: any, row: T) => React.ReactNode
}

interface TableProps<T> {
  columns: Column<T>[]
  data: T[]
  className?: string
}

function Table<T extends { id: string | number }>({
  columns,
  data,
  className,
}: TableProps<T>) {
  return (
    <div className={`overflow-hidden rounded-xl border border-gray-200 ${className}`}>
      <table className="w-full">
        <thead>
          <tr className="bg-gray-50 border-b border-gray-200">
            {columns.map((column) => (
              <th
                key={String(column.key)}
                className={`
                  px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                  text-${column.align || 'left'}
                  ${column.width ? `w-[${column.width}]` : ''}
                `}
              >
                {column.header}
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100">
          {data.map((row) => (
            <tr
              key={row.id}
              className="bg-white hover:bg-gray-50 transition-colors"
            >
              {columns.map((column) => {
                const value = column.key.includes('.')
                  ? getNestedValue(row, column.key)
                  : row[column.key as keyof T]

                return (
                  <td
                    key={String(column.key)}
                    className={`
                      px-4 py-3 font-body text-body-sm text-gray-900
                      text-${column.align || 'left'}
                    `}
                  >
                    {column.render ? column.render(value, row) : String(value ?? '')}
                  </td>
                )
              })}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}

// Helper function to get nested values
function getNestedValue(obj: any, path: string): any {
  return path.split('.').reduce((acc, key) => acc?.[key], obj)
}
```

### Usage Example

```tsx
interface User {
  id: string
  name: string
  email: string
  role: string
  status: 'active' | 'inactive'
}

const columns: Column<User>[] = [
  { key: 'name', header: 'Name' },
  { key: 'email', header: 'Email' },
  { key: 'role', header: 'Role' },
  {
    key: 'status',
    header: 'Status',
    render: (value) => (
      <StatusBadge status={value} />
    ),
  },
]

<Table columns={columns} data={users} />
```

---

## 2. Sortable Columns

Allow users to sort data by clicking column headers.

### Sort States

| State | Icon | Behavior |
|-------|------|----------|
| **Unsorted** | `ChevronUpDown` (muted) | Click to sort ascending |
| **Ascending** | `ChevronUp` (active) | Click to sort descending |
| **Descending** | `ChevronDown` (active) | Click to clear sort |

### Sortable Table Implementation

```tsx
type SortDirection = 'asc' | 'desc' | null

interface SortState {
  key: string
  direction: SortDirection
}

interface SortableColumn<T> extends Column<T> {
  sortable?: boolean
  sortKey?: string
}

interface SortableTableProps<T> {
  columns: SortableColumn<T>[]
  data: T[]
  sortState?: SortState
  onSort?: (state: SortState) => void
}

function SortableTable<T extends { id: string | number }>({
  columns,
  data,
  sortState,
  onSort,
}: SortableTableProps<T>) {
  const handleSort = (column: SortableColumn<T>) => {
    if (!column.sortable || !onSort) return

    const sortKey = column.sortKey || String(column.key)
    let newDirection: SortDirection = 'asc'

    if (sortState?.key === sortKey) {
      if (sortState.direction === 'asc') {
        newDirection = 'desc'
      } else if (sortState.direction === 'desc') {
        newDirection = null
      }
    }

    onSort({ key: sortKey, direction: newDirection })
  }

  return (
    <div className="overflow-hidden rounded-xl border border-gray-200">
      <table className="w-full">
        <thead>
          <tr className="bg-gray-50 border-b border-gray-200">
            {columns.map((column) => {
              const sortKey = column.sortKey || String(column.key)
              const isCurrentSort = sortState?.key === sortKey
              const isSorted = isCurrentSort && sortState?.direction !== null

              return (
                <th
                  key={String(column.key)}
                  className={`
                    px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                    text-${column.align || 'left'}
                    ${column.sortable ? 'cursor-pointer select-none hover:bg-gray-100 transition-colors' : ''}
                  `}
                  onClick={() => handleSort(column)}
                >
                  <div className={`flex items-center gap-1.5 ${column.align === 'right' ? 'justify-end' : ''}`}>
                    {column.header}
                    {column.sortable && (
                      <span className={`flex-shrink-0 ${isSorted ? 'text-mereka-teal' : 'text-gray-400'}`}>
                        {!isSorted && (
                          <ChevronUpDownIcon className="w-4 h-4" />
                        )}
                        {sortState?.direction === 'asc' && isCurrentSort && (
                          <ChevronUpIcon className="w-4 h-4" />
                        )}
                        {sortState?.direction === 'desc' && isCurrentSort && (
                          <ChevronDownIcon className="w-4 h-4" />
                        )}
                      </span>
                    )}
                  </div>
                </th>
              )
            })}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100">
          {data.map((row) => (
            <tr
              key={row.id}
              className="bg-white hover:bg-gray-50 transition-colors"
            >
              {columns.map((column) => {
                const value = column.key.includes('.')
                  ? getNestedValue(row, column.key)
                  : row[column.key as keyof T]

                return (
                  <td
                    key={String(column.key)}
                    className={`
                      px-4 py-3 font-body text-body-sm text-gray-900
                      text-${column.align || 'left'}
                    `}
                  >
                    {column.render ? column.render(value, row) : String(value ?? '')}
                  </td>
                )
              })}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}
```

### Usage Example

```tsx
const [sortState, setSortState] = useState<SortState>({ key: 'name', direction: 'asc' })

const columns: SortableColumn<User>[] = [
  { key: 'name', header: 'Name', sortable: true },
  { key: 'email', header: 'Email', sortable: true },
  { key: 'role', header: 'Role', sortable: true },
  { key: 'status', header: 'Status', sortable: false },
]

<SortableTable
  columns={columns}
  data={sortedUsers}
  sortState={sortState}
  onSort={setSortState}
/>
```

---

## 3. Pagination

Navigate through large datasets with pagination controls.

### Pagination Component

```tsx
interface PaginationProps {
  currentPage: number
  totalPages: number
  totalItems: number
  itemsPerPage: number
  onPageChange: (page: number) => void
  onItemsPerPageChange?: (itemsPerPage: number) => void
  showItemsPerPage?: boolean
  itemsPerPageOptions?: number[]
}

function Pagination({
  currentPage,
  totalPages,
  totalItems,
  itemsPerPage,
  onPageChange,
  onItemsPerPageChange,
  showItemsPerPage = true,
  itemsPerPageOptions = [10, 25, 50, 100],
}: PaginationProps) {
  const startItem = (currentPage - 1) * itemsPerPage + 1
  const endItem = Math.min(currentPage * itemsPerPage, totalItems)

  // Generate page numbers with ellipsis
  const getPageNumbers = () => {
    const pages: (number | 'ellipsis')[] = []
    const showPages = 5

    if (totalPages <= showPages + 2) {
      // Show all pages
      for (let i = 1; i <= totalPages; i++) {
        pages.push(i)
      }
    } else {
      // Always show first page
      pages.push(1)

      if (currentPage > 3) {
        pages.push('ellipsis')
      }

      // Show pages around current
      const start = Math.max(2, currentPage - 1)
      const end = Math.min(totalPages - 1, currentPage + 1)

      for (let i = start; i <= end; i++) {
        pages.push(i)
      }

      if (currentPage < totalPages - 2) {
        pages.push('ellipsis')
      }

      // Always show last page
      pages.push(totalPages)
    }

    return pages
  }

  return (
    <div className="flex flex-col sm:flex-row items-center justify-between gap-4 px-4 py-3 bg-white border-t border-gray-200">
      {/* Items info */}
      <div className="flex items-center gap-4">
        <span className="font-body text-body-sm text-gray-600">
          Showing {startItem} to {endItem} of {totalItems} results
        </span>

        {showItemsPerPage && onItemsPerPageChange && (
          <div className="flex items-center gap-2">
            <span className="font-body text-body-sm text-gray-600">Show:</span>
            <select
              value={itemsPerPage}
              onChange={(e) => onItemsPerPageChange(Number(e.target.value))}
              className="h-8 px-2 pr-8 rounded-lg border border-gray-300 font-body text-body-sm focus:outline-none focus:ring-2 focus:ring-mereka-teal/20 focus:border-mereka-teal"
            >
              {itemsPerPageOptions.map((option) => (
                <option key={option} value={option}>
                  {option}
                </option>
              ))}
            </select>
          </div>
        )}
      </div>

      {/* Page controls */}
      <nav className="flex items-center gap-1" aria-label="Pagination">
        {/* Previous button */}
        <button
          onClick={() => onPageChange(currentPage - 1)}
          disabled={currentPage === 1}
          className="p-2 rounded-lg hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
          aria-label="Previous page"
        >
          <ChevronLeftIcon className="w-5 h-5 text-gray-600" />
        </button>

        {/* Page numbers */}
        <div className="flex items-center gap-1">
          {getPageNumbers().map((page, index) => (
            page === 'ellipsis' ? (
              <span
                key={`ellipsis-${index}`}
                className="w-9 h-9 flex items-center justify-center text-gray-400"
              >
                ...
              </span>
            ) : (
              <button
                key={page}
                onClick={() => onPageChange(page)}
                className={`
                  w-9 h-9 rounded-lg font-body text-body-sm font-medium transition-colors
                  ${currentPage === page
                    ? 'bg-mereka-teal text-white'
                    : 'text-gray-700 hover:bg-gray-100'
                  }
                `}
                aria-label={`Page ${page}`}
                aria-current={currentPage === page ? 'page' : undefined}
              >
                {page}
              </button>
            )
          ))}
        </div>

        {/* Next button */}
        <button
          onClick={() => onPageChange(currentPage + 1)}
          disabled={currentPage === totalPages}
          className="p-2 rounded-lg hover:bg-gray-100 disabled:opacity-50 disabled:cursor-not-allowed transition-colors"
          aria-label="Next page"
        >
          <ChevronRightIcon className="w-5 h-5 text-gray-600" />
        </button>
      </nav>
    </div>
  )
}
```

### Usage Example

```tsx
const [currentPage, setCurrentPage] = useState(1)
const [itemsPerPage, setItemsPerPage] = useState(10)

<div className="rounded-xl border border-gray-200 overflow-hidden">
  <Table columns={columns} data={paginatedData} />
  <Pagination
    currentPage={currentPage}
    totalPages={Math.ceil(totalItems / itemsPerPage)}
    totalItems={totalItems}
    itemsPerPage={itemsPerPage}
    onPageChange={setCurrentPage}
    onItemsPerPageChange={setItemsPerPage}
  />
</div>
```

---

## 4. Row Selection (Checkbox)

Allow selecting individual rows or all rows at once.

### Selection States

| State | Visual | Behavior |
|-------|--------|----------|
| **Unchecked** | Empty checkbox | Row not selected |
| **Checked** | Teal checkbox with check | Row selected |
| **Indeterminate** | Checkbox with dash | Some rows selected (header only) |

### Selectable Table Implementation

```tsx
interface SelectableTableProps<T> {
  columns: Column<T>[]
  data: T[]
  selectedIds: Set<string | number>
  onSelectionChange: (selectedIds: Set<string | number>) => void
  getRowId: (row: T) => string | number
}

function SelectableTable<T>({
  columns,
  data,
  selectedIds,
  onSelectionChange,
  getRowId,
}: SelectableTableProps<T>) {
  const allSelected = data.length > 0 && data.every((row) => selectedIds.has(getRowId(row)))
  const someSelected = data.some((row) => selectedIds.has(getRowId(row)))
  const indeterminate = someSelected && !allSelected

  const toggleAll = () => {
    if (allSelected) {
      // Deselect all
      onSelectionChange(new Set())
    } else {
      // Select all
      onSelectionChange(new Set(data.map(getRowId)))
    }
  }

  const toggleRow = (rowId: string | number) => {
    const newSelection = new Set(selectedIds)
    if (newSelection.has(rowId)) {
      newSelection.delete(rowId)
    } else {
      newSelection.add(rowId)
    }
    onSelectionChange(newSelection)
  }

  return (
    <div className="overflow-hidden rounded-xl border border-gray-200">
      {/* Selection info bar */}
      {selectedIds.size > 0 && (
        <div className="px-4 py-2 bg-mereka-teal/10 border-b border-mereka-teal/20 flex items-center justify-between">
          <span className="font-body text-body-sm text-mereka-teal font-medium">
            {selectedIds.size} {selectedIds.size === 1 ? 'item' : 'items'} selected
          </span>
          <div className="flex items-center gap-2">
            <button
              onClick={() => onSelectionChange(new Set())}
              className="font-body text-body-sm text-mereka-teal hover:underline"
            >
              Clear selection
            </button>
          </div>
        </div>
      )}

      <table className="w-full">
        <thead>
          <tr className="bg-gray-50 border-b border-gray-200">
            {/* Select all checkbox */}
            <th className="w-12 px-4 py-3">
              <Checkbox
                checked={allSelected}
                indeterminate={indeterminate}
                onChange={toggleAll}
                aria-label="Select all rows"
              />
            </th>
            {columns.map((column) => (
              <th
                key={String(column.key)}
                className={`
                  px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                  text-${column.align || 'left'}
                `}
              >
                {column.header}
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100">
          {data.map((row) => {
            const rowId = getRowId(row)
            const isSelected = selectedIds.has(rowId)

            return (
              <tr
                key={rowId}
                className={`
                  transition-colors
                  ${isSelected ? 'bg-mereka-teal/5' : 'bg-white hover:bg-gray-50'}
                `}
              >
                {/* Row checkbox */}
                <td className="w-12 px-4 py-3">
                  <Checkbox
                    checked={isSelected}
                    onChange={() => toggleRow(rowId)}
                    aria-label={`Select row ${rowId}`}
                  />
                </td>
                {columns.map((column) => {
                  const value = column.key.includes('.')
                    ? getNestedValue(row, column.key)
                    : row[column.key as keyof T]

                  return (
                    <td
                      key={String(column.key)}
                      className={`
                        px-4 py-3 font-body text-body-sm text-gray-900
                        text-${column.align || 'left'}
                      `}
                    >
                      {column.render ? column.render(value, row) : String(value ?? '')}
                    </td>
                  )
                })}
              </tr>
            )
          })}
        </tbody>
      </table>
    </div>
  )
}

// Checkbox component with indeterminate support
interface CheckboxProps {
  checked: boolean
  indeterminate?: boolean
  onChange: () => void
  'aria-label'?: string
}

function Checkbox({ checked, indeterminate, onChange, ...props }: CheckboxProps) {
  const ref = React.useRef<HTMLButtonElement>(null)

  React.useEffect(() => {
    if (ref.current) {
      (ref.current as any).indeterminate = indeterminate
    }
  }, [indeterminate])

  return (
    <button
      ref={ref}
      type="button"
      role="checkbox"
      aria-checked={indeterminate ? 'mixed' : checked}
      onClick={onChange}
      className={`
        w-5 h-5 rounded border-2 flex items-center justify-center transition-colors
        ${checked || indeterminate
          ? 'bg-mereka-teal border-mereka-teal'
          : 'bg-white border-gray-300 hover:border-gray-400'
        }
      `}
      {...props}
    >
      {checked && <CheckIcon className="w-3 h-3 text-white" />}
      {indeterminate && !checked && <MinusIcon className="w-3 h-3 text-white" />}
    </button>
  )
}
```

### Usage Example

```tsx
const [selectedIds, setSelectedIds] = useState<Set<string>>(new Set())

<SelectableTable
  columns={columns}
  data={users}
  selectedIds={selectedIds}
  onSelectionChange={setSelectedIds}
  getRowId={(user) => user.id}
/>

{/* Bulk actions */}
{selectedIds.size > 0 && (
  <div className="flex gap-2 mt-4">
    <Button variant="outline" onClick={() => handleBulkDelete(selectedIds)}>
      Delete Selected
    </Button>
    <Button variant="outline" onClick={() => handleBulkExport(selectedIds)}>
      Export Selected
    </Button>
  </div>
)}
```

---

## 5. Row Actions (Edit, Delete, View)

Provide contextual actions for each table row.

### Row Actions Implementation

```tsx
interface RowAction<T> {
  key: string
  label: string
  icon?: React.ReactNode
  onClick: (row: T) => void
  variant?: 'default' | 'danger'
  hidden?: (row: T) => boolean
  disabled?: (row: T) => boolean
}

interface ActionsCellProps<T> {
  row: T
  actions: RowAction<T>[]
}

function ActionsCell<T>({ row, actions }: ActionsCellProps<T>) {
  const [isOpen, setIsOpen] = useState(false)
  const menuRef = useRef<HTMLDivElement>(null)

  // Close menu on click outside
  useEffect(() => {
    const handleClickOutside = (event: MouseEvent) => {
      if (menuRef.current && !menuRef.current.contains(event.target as Node)) {
        setIsOpen(false)
      }
    }
    document.addEventListener('mousedown', handleClickOutside)
    return () => document.removeEventListener('mousedown', handleClickOutside)
  }, [])

  const visibleActions = actions.filter((action) => !action.hidden?.(row))

  // If 3 or fewer actions, show them inline
  if (visibleActions.length <= 3) {
    return (
      <div className="flex items-center justify-end gap-1">
        {visibleActions.map((action) => (
          <button
            key={action.key}
            onClick={() => action.onClick(row)}
            disabled={action.disabled?.(row)}
            className={`
              p-2 rounded-lg transition-colors
              ${action.variant === 'danger'
                ? 'text-gray-400 hover:text-mereka-burgundy hover:bg-mereka-burgundy/10'
                : 'text-gray-400 hover:text-gray-700 hover:bg-gray-100'
              }
              disabled:opacity-50 disabled:cursor-not-allowed
            `}
            title={action.label}
            aria-label={action.label}
          >
            {action.icon || action.label}
          </button>
        ))}
      </div>
    )
  }

  // If more actions, use dropdown menu
  return (
    <div className="relative" ref={menuRef}>
      <button
        onClick={() => setIsOpen(!isOpen)}
        className="p-2 rounded-lg text-gray-400 hover:text-gray-700 hover:bg-gray-100 transition-colors"
        aria-label="More actions"
        aria-expanded={isOpen}
        aria-haspopup="menu"
      >
        <MoreVerticalIcon className="w-5 h-5" />
      </button>

      {isOpen && (
        <div className="absolute right-0 top-full mt-1 z-50 min-w-[160px] bg-white rounded-lg border border-gray-200 shadow-lg py-1">
          {visibleActions.map((action) => (
            <button
              key={action.key}
              onClick={() => {
                action.onClick(row)
                setIsOpen(false)
              }}
              disabled={action.disabled?.(row)}
              className={`
                w-full flex items-center gap-2 px-4 py-2 font-body text-body-sm text-left transition-colors
                ${action.variant === 'danger'
                  ? 'text-mereka-burgundy hover:bg-mereka-burgundy/10'
                  : 'text-gray-700 hover:bg-gray-50'
                }
                disabled:opacity-50 disabled:cursor-not-allowed
              `}
            >
              {action.icon}
              {action.label}
            </button>
          ))}
        </div>
      )}
    </div>
  )
}
```

### Usage Example

```tsx
const userActions: RowAction<User>[] = [
  {
    key: 'view',
    label: 'View',
    icon: <EyeIcon className="w-4 h-4" />,
    onClick: (user) => navigate(`/users/${user.id}`),
  },
  {
    key: 'edit',
    label: 'Edit',
    icon: <PencilIcon className="w-4 h-4" />,
    onClick: (user) => openEditModal(user),
  },
  {
    key: 'delete',
    label: 'Delete',
    icon: <TrashIcon className="w-4 h-4" />,
    onClick: (user) => confirmDelete(user),
    variant: 'danger',
    hidden: (user) => user.role === 'admin',
  },
]

// Add actions column to your table
const columns: Column<User>[] = [
  { key: 'name', header: 'Name' },
  { key: 'email', header: 'Email' },
  {
    key: 'actions',
    header: '',
    align: 'right',
    render: (_, row) => <ActionsCell row={row} actions={userActions} />,
  },
]
```

---

## 6. Empty State

Display when no data is available or filters return no results.

### Empty State Implementation

```tsx
interface EmptyStateProps {
  icon?: React.ReactNode
  title: string
  description?: string
  action?: {
    label: string
    onClick: () => void
  }
  variant?: 'default' | 'filtered'
}

function TableEmptyState({
  icon,
  title,
  description,
  action,
  variant = 'default',
}: EmptyStateProps) {
  return (
    <div className="flex flex-col items-center justify-center py-16 px-4">
      <div className={`
        w-16 h-16 rounded-full flex items-center justify-center mb-4
        ${variant === 'filtered' ? 'bg-mereka-sky/30' : 'bg-gray-100'}
      `}>
        {icon || (
          variant === 'filtered'
            ? <SearchIcon className="w-8 h-8 text-mereka-blue" />
            : <InboxIcon className="w-8 h-8 text-gray-400" />
        )}
      </div>

      <h3 className="font-heading text-h4 text-gray-900 text-center">
        {title}
      </h3>

      {description && (
        <p className="font-body text-body-sm text-gray-600 text-center mt-2 max-w-sm">
          {description}
        </p>
      )}

      {action && (
        <button
          onClick={action.onClick}
          className="mt-4 inline-flex items-center gap-2 px-4 py-2 bg-mereka-teal text-white rounded-full font-body text-body-sm font-medium hover:bg-mereka-teal/90 transition-colors"
        >
          {action.label}
        </button>
      )}
    </div>
  )
}
```

### Usage Examples

```tsx
// No data at all
{data.length === 0 && !hasFilters && (
  <TableEmptyState
    icon={<UsersIcon className="w-8 h-8 text-gray-400" />}
    title="No users yet"
    description="Get started by adding your first user to the system."
    action={{
      label: "Add User",
      onClick: () => openAddUserModal(),
    }}
  />
)}

// No results from filter
{data.length === 0 && hasFilters && (
  <TableEmptyState
    variant="filtered"
    title="No results found"
    description="Try adjusting your search or filter criteria."
    action={{
      label: "Clear Filters",
      onClick: () => clearFilters(),
    }}
  />
)}
```

---

## 7. Loading State (Skeleton Rows)

Display skeleton rows while data is loading.

### Skeleton Row Implementation

```tsx
interface TableSkeletonProps {
  columns: number
  rows?: number
  showCheckbox?: boolean
}

function TableSkeleton({
  columns,
  rows = 5,
  showCheckbox = false,
}: TableSkeletonProps) {
  return (
    <div className="overflow-hidden rounded-xl border border-gray-200">
      <table className="w-full">
        <thead>
          <tr className="bg-gray-50 border-b border-gray-200">
            {showCheckbox && (
              <th className="w-12 px-4 py-3">
                <div className="w-5 h-5 bg-gray-200 rounded animate-pulse" />
              </th>
            )}
            {Array.from({ length: columns }).map((_, i) => (
              <th key={i} className="px-4 py-3">
                <div className="h-4 bg-gray-200 rounded w-24 animate-pulse" />
              </th>
            ))}
          </tr>
        </thead>
        <tbody className="divide-y divide-gray-100">
          {Array.from({ length: rows }).map((_, rowIndex) => (
            <tr key={rowIndex} className="bg-white">
              {showCheckbox && (
                <td className="w-12 px-4 py-3">
                  <div className="w-5 h-5 bg-gray-200 rounded animate-pulse" />
                </td>
              )}
              {Array.from({ length: columns }).map((_, colIndex) => (
                <td key={colIndex} className="px-4 py-3">
                  <div
                    className="h-4 bg-gray-200 rounded animate-pulse"
                    style={{
                      width: `${Math.random() * 40 + 60}%`,
                      animationDelay: `${(rowIndex * columns + colIndex) * 50}ms`,
                    }}
                  />
                </td>
              ))}
            </tr>
          ))}
        </tbody>
      </table>
    </div>
  )
}
```

### Usage Example

```tsx
function UsersTable() {
  const { data, isLoading } = useUsers()

  if (isLoading) {
    return <TableSkeleton columns={5} rows={10} showCheckbox />
  }

  return <Table columns={columns} data={data} />
}
```

---

## 8. Responsive Behavior

Tables adapt to smaller screens with horizontal scroll or card view.

### Option 1: Horizontal Scroll

```tsx
function ResponsiveScrollTable<T extends { id: string | number }>({
  columns,
  data,
}: TableProps<T>) {
  return (
    <div className="overflow-hidden rounded-xl border border-gray-200">
      {/* Scroll hint on mobile */}
      <div className="md:hidden px-4 py-2 bg-gray-50 border-b border-gray-200 flex items-center gap-2 text-caption text-gray-500">
        <ArrowLeftRightIcon className="w-4 h-4" />
        Scroll horizontally to see more
      </div>

      <div className="overflow-x-auto">
        <table className="w-full min-w-[640px]">
          <thead>
            <tr className="bg-gray-50 border-b border-gray-200">
              {columns.map((column) => (
                <th
                  key={String(column.key)}
                  className={`
                    px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                    text-${column.align || 'left'} whitespace-nowrap
                  `}
                >
                  {column.header}
                </th>
              ))}
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {data.map((row) => (
              <tr key={row.id} className="bg-white hover:bg-gray-50 transition-colors">
                {columns.map((column) => {
                  const value = row[column.key as keyof T]
                  return (
                    <td
                      key={String(column.key)}
                      className={`
                        px-4 py-3 font-body text-body-sm text-gray-900
                        text-${column.align || 'left'} whitespace-nowrap
                      `}
                    >
                      {column.render ? column.render(value, row) : String(value ?? '')}
                    </td>
                  )
                })}
              </tr>
            ))}
          </tbody>
        </table>
      </div>
    </div>
  )
}
```

### Option 2: Card View on Mobile

```tsx
interface ResponsiveTableProps<T> {
  columns: Column<T>[]
  data: T[]
  primaryColumn: keyof T
  secondaryColumn?: keyof T
  renderMobileCard?: (row: T) => React.ReactNode
}

function ResponsiveTable<T extends { id: string | number }>({
  columns,
  data,
  primaryColumn,
  secondaryColumn,
  renderMobileCard,
}: ResponsiveTableProps<T>) {
  return (
    <>
      {/* Desktop table */}
      <div className="hidden md:block overflow-hidden rounded-xl border border-gray-200">
        <table className="w-full">
          <thead>
            <tr className="bg-gray-50 border-b border-gray-200">
              {columns.map((column) => (
                <th
                  key={String(column.key)}
                  className={`
                    px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                    text-${column.align || 'left'}
                  `}
                >
                  {column.header}
                </th>
              ))}
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {data.map((row) => (
              <tr key={row.id} className="bg-white hover:bg-gray-50 transition-colors">
                {columns.map((column) => {
                  const value = row[column.key as keyof T]
                  return (
                    <td
                      key={String(column.key)}
                      className={`
                        px-4 py-3 font-body text-body-sm text-gray-900
                        text-${column.align || 'left'}
                      `}
                    >
                      {column.render ? column.render(value, row) : String(value ?? '')}
                    </td>
                  )
                })}
              </tr>
            ))}
          </tbody>
        </table>
      </div>

      {/* Mobile card view */}
      <div className="md:hidden space-y-3">
        {data.map((row) => (
          renderMobileCard ? (
            <div key={row.id}>{renderMobileCard(row)}</div>
          ) : (
            <div
              key={row.id}
              className="bg-white rounded-xl border border-gray-200 p-4 space-y-3"
            >
              {/* Primary info */}
              <div className="flex items-start justify-between">
                <div>
                  <p className="font-heading text-body font-semibold text-gray-900">
                    {String(row[primaryColumn])}
                  </p>
                  {secondaryColumn && (
                    <p className="font-body text-body-sm text-gray-600 mt-0.5">
                      {String(row[secondaryColumn])}
                    </p>
                  )}
                </div>
              </div>

              {/* Other columns */}
              <div className="grid grid-cols-2 gap-3 pt-3 border-t border-gray-100">
                {columns
                  .filter((col) => col.key !== primaryColumn && col.key !== secondaryColumn && col.key !== 'actions')
                  .map((column) => {
                    const value = row[column.key as keyof T]
                    return (
                      <div key={String(column.key)}>
                        <p className="font-body text-caption text-gray-500">
                          {column.header}
                        </p>
                        <p className="font-body text-body-sm text-gray-900 mt-0.5">
                          {column.render ? column.render(value, row) : String(value ?? '')}
                        </p>
                      </div>
                    )
                  })}
              </div>

              {/* Actions */}
              {columns.find((col) => col.key === 'actions') && (
                <div className="pt-3 border-t border-gray-100">
                  {columns.find((col) => col.key === 'actions')?.render?.(null, row)}
                </div>
              )}
            </div>
          )
        ))}
      </div>
    </>
  )
}
```

---

## Example Tables

### Users Table

```tsx
interface User {
  id: string
  name: string
  email: string
  role: 'admin' | 'hub_owner' | 'learner'
  status: 'active' | 'inactive' | 'pending'
  avatar?: string
  createdAt: Date
}

function UsersTable({ users }: { users: User[] }) {
  const [selectedIds, setSelectedIds] = useState<Set<string>>(new Set())
  const [sortState, setSortState] = useState<SortState>({ key: 'name', direction: 'asc' })

  const columns: SortableColumn<User>[] = [
    {
      key: 'name',
      header: 'Name',
      sortable: true,
      render: (_, user) => (
        <div className="flex items-center gap-3">
          {user.avatar ? (
            <img src={user.avatar} alt="" className="w-8 h-8 rounded-full object-cover" />
          ) : (
            <div className="w-8 h-8 rounded-full bg-gray-200 flex items-center justify-center">
              <span className="font-heading text-body-sm text-gray-600">
                {user.name.charAt(0).toUpperCase()}
              </span>
            </div>
          )}
          <span className="font-medium">{user.name}</span>
        </div>
      ),
    },
    {
      key: 'email',
      header: 'Email',
      sortable: true,
    },
    {
      key: 'role',
      header: 'Role',
      sortable: true,
      render: (role) => (
        <span className="capitalize">{role.replace('_', ' ')}</span>
      ),
    },
    {
      key: 'status',
      header: 'Status',
      sortable: true,
      render: (status) => <StatusBadge status={status} />,
    },
    {
      key: 'actions',
      header: '',
      align: 'right',
      render: (_, user) => (
        <ActionsCell
          row={user}
          actions={[
            { key: 'view', label: 'View', icon: <EyeIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'edit', label: 'Edit', icon: <PencilIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'delete', label: 'Delete', icon: <TrashIcon className="w-4 h-4" />, onClick: () => {}, variant: 'danger' },
          ]}
        />
      ),
    },
  ]

  return (
    <SelectableTable
      columns={columns}
      data={users}
      selectedIds={selectedIds}
      onSelectionChange={setSelectedIds}
      getRowId={(user) => user.id}
    />
  )
}

// Status Badge Component
function StatusBadge({ status }: { status: string }) {
  const styles = {
    active: 'bg-mereka-mint text-mereka-forest',
    inactive: 'bg-gray-100 text-gray-600',
    pending: 'bg-mereka-gold/20 text-mereka-orange',
    confirmed: 'bg-mereka-mint text-mereka-forest',
    cancelled: 'bg-mereka-pink text-mereka-burgundy',
    completed: 'bg-mereka-sky/50 text-mereka-blue',
    draft: 'bg-gray-100 text-gray-600',
    published: 'bg-mereka-mint text-mereka-forest',
  }

  return (
    <span className={`
      inline-flex items-center px-2.5 py-0.5 rounded-full
      font-body text-caption font-medium capitalize
      ${styles[status as keyof typeof styles] || 'bg-gray-100 text-gray-600'}
    `}>
      {status}
    </span>
  )
}
```

### Bookings Table

```tsx
interface Booking {
  id: string
  experience: {
    title: string
    coverImage: string
  }
  learner: {
    name: string
    email: string
  }
  date: Date
  status: 'confirmed' | 'pending' | 'cancelled' | 'completed'
  amount: number
  currency: string
}

function BookingsTable({ bookings }: { bookings: Booking[] }) {
  const columns: Column<Booking>[] = [
    {
      key: 'experience.title',
      header: 'Experience',
      render: (_, booking) => (
        <div className="flex items-center gap-3">
          <img
            src={booking.experience.coverImage}
            alt=""
            className="w-10 h-10 rounded-lg object-cover"
          />
          <span className="font-medium line-clamp-1">{booking.experience.title}</span>
        </div>
      ),
    },
    {
      key: 'learner.name',
      header: 'Learner',
      render: (_, booking) => (
        <div>
          <p className="font-medium">{booking.learner.name}</p>
          <p className="text-caption text-gray-500">{booking.learner.email}</p>
        </div>
      ),
    },
    {
      key: 'date',
      header: 'Date',
      render: (date) => (
        <span>{new Date(date).toLocaleDateString('en-MY', {
          day: 'numeric',
          month: 'short',
          year: 'numeric',
        })}</span>
      ),
    },
    {
      key: 'status',
      header: 'Status',
      render: (status) => <StatusBadge status={status} />,
    },
    {
      key: 'amount',
      header: 'Amount',
      align: 'right',
      render: (_, booking) => (
        <span className="font-medium">
          {booking.currency} {booking.amount.toLocaleString()}
        </span>
      ),
    },
    {
      key: 'actions',
      header: '',
      align: 'right',
      render: (_, booking) => (
        <ActionsCell
          row={booking}
          actions={[
            { key: 'view', label: 'View Details', icon: <EyeIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'cancel', label: 'Cancel Booking', icon: <XIcon className="w-4 h-4" />, onClick: () => {}, variant: 'danger', hidden: (b) => b.status === 'cancelled' || b.status === 'completed' },
          ]}
        />
      ),
    },
  ]

  return <Table columns={columns} data={bookings} />
}
```

### Experiences Table

```tsx
interface Experience {
  id: string
  title: string
  coverImage: string
  hub: {
    name: string
    logo: string
  }
  date: Date
  bookings: number
  capacity: number
  status: 'draft' | 'published' | 'cancelled'
}

function ExperiencesTable({ experiences }: { experiences: Experience[] }) {
  const columns: Column<Experience>[] = [
    {
      key: 'title',
      header: 'Title',
      render: (_, exp) => (
        <div className="flex items-center gap-3">
          <img
            src={exp.coverImage}
            alt=""
            className="w-12 h-8 rounded object-cover"
          />
          <span className="font-medium line-clamp-1">{exp.title}</span>
        </div>
      ),
    },
    {
      key: 'hub.name',
      header: 'Hub',
      render: (_, exp) => (
        <div className="flex items-center gap-2">
          <img src={exp.hub.logo} alt="" className="w-6 h-6 rounded" />
          <span>{exp.hub.name}</span>
        </div>
      ),
    },
    {
      key: 'date',
      header: 'Date',
      render: (date) => (
        <span>{new Date(date).toLocaleDateString('en-MY', {
          day: 'numeric',
          month: 'short',
          year: 'numeric',
        })}</span>
      ),
    },
    {
      key: 'bookings',
      header: 'Bookings',
      render: (_, exp) => (
        <div className="flex items-center gap-2">
          <span>{exp.bookings}/{exp.capacity}</span>
          <div className="w-16 h-1.5 bg-gray-200 rounded-full overflow-hidden">
            <div
              className="h-full bg-mereka-teal rounded-full"
              style={{ width: `${(exp.bookings / exp.capacity) * 100}%` }}
            />
          </div>
        </div>
      ),
    },
    {
      key: 'status',
      header: 'Status',
      render: (status) => <StatusBadge status={status} />,
    },
    {
      key: 'actions',
      header: '',
      align: 'right',
      render: (_, exp) => (
        <ActionsCell
          row={exp}
          actions={[
            { key: 'view', label: 'View', icon: <EyeIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'edit', label: 'Edit', icon: <PencilIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'duplicate', label: 'Duplicate', icon: <CopyIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'delete', label: 'Delete', icon: <TrashIcon className="w-4 h-4" />, onClick: () => {}, variant: 'danger' },
          ]}
        />
      ),
    },
  ]

  return <Table columns={columns} data={experiences} />
}
```

### Transactions Table

```tsx
interface Transaction {
  id: string
  type: 'payment' | 'refund' | 'payout'
  description: string
  amount: number
  currency: string
  status: 'completed' | 'pending' | 'failed'
  date: Date
}

function TransactionsTable({ transactions }: { transactions: Transaction[] }) {
  const columns: Column<Transaction>[] = [
    {
      key: 'id',
      header: 'Transaction ID',
      render: (id) => (
        <span className="font-mono text-body-sm">{id}</span>
      ),
    },
    {
      key: 'type',
      header: 'Type',
      render: (type) => {
        const icons = {
          payment: <ArrowDownIcon className="w-4 h-4 text-mereka-forest" />,
          refund: <ArrowUpIcon className="w-4 h-4 text-mereka-burgundy" />,
          payout: <ArrowRightIcon className="w-4 h-4 text-mereka-blue" />,
        }
        return (
          <div className="flex items-center gap-2 capitalize">
            {icons[type as keyof typeof icons]}
            {type}
          </div>
        )
      },
    },
    {
      key: 'amount',
      header: 'Amount',
      align: 'right',
      render: (_, tx) => (
        <span className={`font-medium ${tx.type === 'refund' ? 'text-mereka-burgundy' : ''}`}>
          {tx.type === 'refund' ? '-' : ''}{tx.currency} {tx.amount.toLocaleString()}
        </span>
      ),
    },
    {
      key: 'status',
      header: 'Status',
      render: (status) => <StatusBadge status={status} />,
    },
    {
      key: 'date',
      header: 'Date',
      render: (date) => (
        <span>{new Date(date).toLocaleDateString('en-MY', {
          day: 'numeric',
          month: 'short',
          year: 'numeric',
          hour: '2-digit',
          minute: '2-digit',
        })}</span>
      ),
    },
    {
      key: 'actions',
      header: '',
      align: 'right',
      render: (_, tx) => (
        <ActionsCell
          row={tx}
          actions={[
            { key: 'view', label: 'View Details', icon: <EyeIcon className="w-4 h-4" />, onClick: () => {} },
            { key: 'download', label: 'Download Receipt', icon: <DownloadIcon className="w-4 h-4" />, onClick: () => {} },
          ]}
        />
      ),
    },
  ]

  return <Table columns={columns} data={transactions} />
}
```

---

## Complete Data Table Component

A full-featured table combining all capabilities.

```tsx
interface DataTableProps<T> {
  // Data
  columns: SortableColumn<T>[]
  data: T[]
  getRowId: (row: T) => string | number

  // Loading/Empty states
  isLoading?: boolean
  emptyState?: {
    title: string
    description?: string
    action?: { label: string; onClick: () => void }
  }

  // Sorting
  sortable?: boolean
  defaultSort?: SortState

  // Selection
  selectable?: boolean
  selectedIds?: Set<string | number>
  onSelectionChange?: (ids: Set<string | number>) => void

  // Pagination
  pagination?: {
    currentPage: number
    totalPages: number
    totalItems: number
    itemsPerPage: number
    onPageChange: (page: number) => void
    onItemsPerPageChange?: (itemsPerPage: number) => void
  }

  // Responsive
  responsive?: 'scroll' | 'cards'
  primaryColumn?: keyof T
}

function DataTable<T>({
  columns,
  data,
  getRowId,
  isLoading,
  emptyState,
  sortable = false,
  defaultSort,
  selectable = false,
  selectedIds = new Set(),
  onSelectionChange,
  pagination,
  responsive = 'scroll',
  primaryColumn,
}: DataTableProps<T>) {
  const [sortState, setSortState] = useState<SortState | undefined>(defaultSort)

  if (isLoading) {
    return <TableSkeleton columns={columns.length} rows={pagination?.itemsPerPage || 10} showCheckbox={selectable} />
  }

  if (data.length === 0 && emptyState) {
    return (
      <div className="rounded-xl border border-gray-200">
        <TableEmptyState {...emptyState} />
      </div>
    )
  }

  return (
    <div className="rounded-xl border border-gray-200 overflow-hidden">
      {/* Selection info bar */}
      {selectable && selectedIds.size > 0 && (
        <div className="px-4 py-2 bg-mereka-teal/10 border-b border-mereka-teal/20 flex items-center justify-between">
          <span className="font-body text-body-sm text-mereka-teal font-medium">
            {selectedIds.size} {selectedIds.size === 1 ? 'item' : 'items'} selected
          </span>
          <button
            onClick={() => onSelectionChange?.(new Set())}
            className="font-body text-body-sm text-mereka-teal hover:underline"
          >
            Clear selection
          </button>
        </div>
      )}

      {/* Table */}
      <div className={responsive === 'scroll' ? 'overflow-x-auto' : ''}>
        <table className="w-full min-w-[640px]">
          <thead>
            <tr className="bg-gray-50 border-b border-gray-200">
              {selectable && (
                <th className="w-12 px-4 py-3">
                  <Checkbox
                    checked={data.length > 0 && data.every((row) => selectedIds.has(getRowId(row)))}
                    indeterminate={data.some((row) => selectedIds.has(getRowId(row))) && !data.every((row) => selectedIds.has(getRowId(row)))}
                    onChange={() => {
                      const allSelected = data.every((row) => selectedIds.has(getRowId(row)))
                      onSelectionChange?.(allSelected ? new Set() : new Set(data.map(getRowId)))
                    }}
                    aria-label="Select all rows"
                  />
                </th>
              )}
              {columns.map((column) => (
                <th
                  key={String(column.key)}
                  className={`
                    px-4 py-3 font-heading text-body-sm font-semibold text-gray-700
                    text-${column.align || 'left'}
                    ${column.sortable ? 'cursor-pointer select-none hover:bg-gray-100 transition-colors' : ''}
                  `}
                  onClick={() => {
                    if (!column.sortable) return
                    const sortKey = column.sortKey || String(column.key)
                    let newDirection: SortDirection = 'asc'
                    if (sortState?.key === sortKey) {
                      if (sortState.direction === 'asc') newDirection = 'desc'
                      else if (sortState.direction === 'desc') newDirection = null
                    }
                    setSortState({ key: sortKey, direction: newDirection })
                  }}
                >
                  <div className={`flex items-center gap-1.5 ${column.align === 'right' ? 'justify-end' : ''}`}>
                    {column.header}
                    {column.sortable && (
                      <SortIndicator
                        direction={sortState?.key === (column.sortKey || String(column.key)) ? sortState.direction : null}
                      />
                    )}
                  </div>
                </th>
              ))}
            </tr>
          </thead>
          <tbody className="divide-y divide-gray-100">
            {data.map((row) => {
              const rowId = getRowId(row)
              const isSelected = selectedIds.has(rowId)

              return (
                <tr
                  key={rowId}
                  className={`
                    transition-colors
                    ${isSelected ? 'bg-mereka-teal/5' : 'bg-white hover:bg-gray-50'}
                  `}
                >
                  {selectable && (
                    <td className="w-12 px-4 py-3">
                      <Checkbox
                        checked={isSelected}
                        onChange={() => {
                          const newSelection = new Set(selectedIds)
                          if (isSelected) newSelection.delete(rowId)
                          else newSelection.add(rowId)
                          onSelectionChange?.(newSelection)
                        }}
                        aria-label={`Select row ${rowId}`}
                      />
                    </td>
                  )}
                  {columns.map((column) => {
                    const value = column.key.includes('.')
                      ? getNestedValue(row, column.key)
                      : row[column.key as keyof T]

                    return (
                      <td
                        key={String(column.key)}
                        className={`
                          px-4 py-3 font-body text-body-sm text-gray-900
                          text-${column.align || 'left'}
                        `}
                      >
                        {column.render ? column.render(value, row) : String(value ?? '')}
                      </td>
                    )
                  })}
                </tr>
              )
            })}
          </tbody>
        </table>
      </div>

      {/* Pagination */}
      {pagination && (
        <Pagination {...pagination} />
      )}
    </div>
  )
}

// Sort indicator component
function SortIndicator({ direction }: { direction: SortDirection }) {
  return (
    <span className={`flex-shrink-0 ${direction ? 'text-mereka-teal' : 'text-gray-400'}`}>
      {!direction && <ChevronUpDownIcon className="w-4 h-4" />}
      {direction === 'asc' && <ChevronUpIcon className="w-4 h-4" />}
      {direction === 'desc' && <ChevronDownIcon className="w-4 h-4" />}
    </span>
  )
}
```

---

## Accessibility

### Keyboard Navigation

```tsx
// Table rows should be focusable when interactive
<tr
  tabIndex={0}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleRowClick(row)
    }
  }}
/>

// Sort buttons should be keyboard accessible
<th
  role="columnheader"
  aria-sort={sortState?.key === column.key ? (sortState.direction === 'asc' ? 'ascending' : 'descending') : 'none'}
  tabIndex={column.sortable ? 0 : -1}
  onKeyDown={(e) => {
    if (e.key === 'Enter' || e.key === ' ') {
      handleSort(column)
    }
  }}
/>
```

### Screen Reader Support

```tsx
// Announce selection changes
<div role="status" aria-live="polite" className="sr-only">
  {selectedIds.size} items selected
</div>

// Pagination info
<nav aria-label="Pagination">
  <span className="sr-only">Page {currentPage} of {totalPages}</span>
</nav>

// Empty state
<div role="status" aria-label="No data available">
  <TableEmptyState ... />
</div>
```

---

## Do's and Don'ts

### Do's

- **Do** use consistent column widths within a table
- **Do** provide visual feedback on sortable column headers
- **Do** show loading skeletons while fetching data
- **Do** include clear empty states with actionable guidance
- **Do** use semantic table markup (`<table>`, `<thead>`, `<tbody>`)
- **Do** make row actions accessible via keyboard
- **Do** truncate long text with ellipsis and provide full text on hover
- **Do** align numeric data to the right for easier scanning
- **Do** use status badges for categorical data
- **Do** provide bulk action capabilities with row selection

### Don'ts

- **Don't** display more than 7-8 columns without horizontal scroll
- **Don't** use tables for non-tabular data
- **Don't** hide important actions in dropdown menus on desktop
- **Don't** forget to handle empty and loading states
- **Don't** use inconsistent status badge colors
- **Don't** make entire rows clickable without clear indication
- **Don't** paginate with fewer than 10 items
- **Don't** sort by multiple columns simultaneously (confusing UX)
- **Don't** remove pagination controls when there's only one page
- **Don't** use zebra striping (alternating row colors) - use hover states instead

---

## Related Documentation

- [Buttons](./buttons.md) - Action buttons and pagination controls
- [Inputs](./inputs.md) - Checkbox and select components
- [Cards](./cards.md) - Mobile card view patterns
- [Colors](./colors.md) - Status badge colors
- [Typography](./typography.md) - Table text styles
