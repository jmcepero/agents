---
agent: agent
---

# Expo Code Organizer

Reorganize React Native components and custom hooks following a standardized structure for **Expo** multiplataform projects.

**Goal**: Structure reorganization only. Preserve all existing logic, functionality, and patterns as-is.

---

## Organization Rules

### For Components:

1. **State management** - Store hooks, selectors, or state subscriptions
2. **Navigation hooks** - `useRouter()`, `useNavigation()`, `useLocalSearchParams()`, etc.
3. **Context hooks** - `useTheme()`, `useSafeAreaInsets()`, etc.
4. **Local state** - `useState()`, `useReducer()` for component-specific state
5. **Refs** - `useRef()` declarations for DOM references, animated values, etc.
6. **Computed/derived values** - Memoized calculations, derived state from props/store
7. **Handlers** - Event handlers and callbacks (use `useCallback` when appropriate)
8. **Custom hooks** - Feature-specific hook invocations
9. **Effects** - `useEffect()`, `useFocusEffect()` declarations
10. **Render helpers** _(optional)_ - Helper functions that return JSX fragments
11. **Main render** - The component's return statement

### For Custom Hooks:

1. **Imports** - All dependencies (React, Expo Router, stores, utilities)
2. **Parameters** - Hook input parameters and types
3. **State management** - Store hooks or subscriptions if needed
4. **Internal state** - Hook-specific useState/useReducer
5. **Refs** - Stable references (e.g., abort controllers, timers)
6. **Computed values** - Derived data from params/state
7. **Handlers** - Internal callbacks
8. **Side effects** - useEffect/useFocusEffect for initialization/cleanup
9. **Return value** - Exposed API object

---

## Formatting Guidelines

- Use **simple numbered comments** for each section (e.g., `// 1. State management`)
- **DO NOT** use heavy delimiters like `====` or `----`
- Keep comments concise and descriptive
- Add blank line after each section for visual separation

---

## Component Example

```typescript
export const DataListScreen = () => {
  // 1. State management
  const dataListStore = useDataListStore();

  // 2. Navigation hooks
  const router = useRouter();
  const { category } = useLocalSearchParams();

  // 3. Context hooks
  const theme = useTheme();
  const insets = useSafeAreaInsets();

  // 4. Local state
  const [refreshing, setRefreshing] = useState(false);

  // 5. Computed/derived values
  const styles = createStyles(theme.colors, insets);
  const hasData = dataListStore.items.length > 0;

  // 6. Handlers
  const handleItemPress = (item: DataItem) => {
    dataListStore.selectItem(item.id);
  };

  // 7. Custom hooks
  useDataListEvents(dataListStore);

  // 8. Effects
  useEffect(() => {
    dataListStore.initialize(category);
    return () => dataListStore.cleanup();
  }, [dataListStore, category]);

  // 9. Render helpers (optional)
  const renderItem = ({ item }) => (
    <DataCard data={item} onPress={() => handleItemPress(item)} />
  );

  // 10. Main render
  return (
    <ScreenWrapper>
      <FlatList data={dataListStore.items} renderItem={renderItem} />
    </ScreenWrapper>
  );
};
```

---

## Custom Hook Example

```typescript
export const useDataFetch = (options: UseDataFetchOptions = {}) => {
  // 1. Parameters destructuring
  const { fetchOnMount = true } = options;

  // 2. State management
  const dataStore = useDataStore();

  // 3. Internal state
  const [isInitialized, setIsInitialized] = useState(false);

  // 4. Refs
  const isMountedRef = useRef(true);

  // 5. Computed values
  const hasData = dataStore.items.length > 0;

  // 6. Handlers
  const fetchData = async () => {
    if (!isMountedRef.current) return;
    await dataStore.fetchData();
  };

  // 7. Side effects
  useEffect(() => {
    if (fetchOnMount && !isInitialized) {
      fetchData();
      setIsInitialized(true);
    }
    return () => {
      isMountedRef.current = false;
      dataStore.reset();
    };
  }, [fetchOnMount, isInitialized]);

  // 8. Return value
  return {
    data: dataStore.items,
    isLoading: dataStore.isLoading,
    hasData,
    refetch: fetchData,
  };
};
```

---

## Instructions

- Skip sections that don't apply to your component
- For simple components, use judgment - don't over-structure
- **Preserve all existing functionality** - this is structural reorganization only
- Keep all business logic, comments, types, and JSDoc as-is
- Do not modify dependency arrays, hook logic, or patterns
- Do not analyze or suggest improvements

---

## Output Format

Provide only the reorganized code following the structure above, without explanations.
