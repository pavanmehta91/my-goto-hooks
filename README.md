# my-goto-hooks
custom hooks, self-built, copied or inspired But useful nonetheless
## 1. KeyPress Hook which returns a true flag
```
function useKeyPress(targetKey: string) {
  // State for keeping track of whether key is pressed

  const [keyPressed, setKeyPressed] = useState(false);

  // If pressed key is our target key then set to true

  function downHandler(event: KeyboardEvent) {
    const { key } = event;
    if (key === targetKey) {
      setKeyPressed(true);
    }
  }

  // If released key is our target key then set to false

  const upHandler = (event: KeyboardEvent) => {
    const { key } = event;
    if (key === targetKey) {
      setKeyPressed(false);
    }
  };

  // Add event listeners

  useEffect(() => {
    window.addEventListener("keydown", downHandler);

    window.addEventListener("keyup", upHandler);

    // Remove event listeners on cleanup

    return () => {
      window.removeEventListener("keydown", downHandler);

      window.removeEventListener("keyup", upHandler);
    };
  }, []); // Empty array ensures that effect is only run on mount and unmount

  return keyPressed;
}
```
Example usage

```
  const isEspacePressed = useKeyPress("Escape");
  useEffect(() => {
    if (isEspacePressed) doSomething();
  }, [isEspacePressed]);
```
## 2. Use Local Storage
```
function useLocalStorage(key, initialValue) {
  // State to store our value
  // Pass initial state function to useState so logic is only executed once
  const [storedValue, setStoredValue] = useState(() => {
    try {
      // Get from local storage by key
      const item = window.localStorage.getItem(key);
      // Parse stored json or if none return initialValue
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      // If error also return initialValue
      console.error(error);
      return initialValue;
    }
  });

  // Return a wrapped version of useState's setter function that ...
  // ... persists the new value to localStorage.
  const setValue = value => {
    try {
      // Allow value to be a function so we have same API as useState
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      // Save state
      setStoredValue(valueToStore);
      // Save to local storage
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      // A more advanced implementation would handle the error case
      console.error(error);
    }
  };

  return [storedValue, setValue];
}
```
Example Usage
```const [activeTab, setActiveTab] = useLocalStorage('activeTab', defaultActiveTab);```
## 3. UsePrevious Value of something
```
function usePrevious(value, deps = [value]) {
  // The ref object is a generic container whose current property is mutable ...

  // ... and can hold any value, similar to an instance property on a class

  const ref = useRef();

  // Store current value in ref

  useEffect(() => {
    ref.current = value;
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, deps); // Only re-run if value changes

  // Return previous value (happens before update in useEffect above)

  return ref.current;
}
```
Example usage
```
  const previousField = usePrevious(current_field);
  //mimicking useInitial.. preseves values changes in the current field
  const initialContent = usePrevious(current_field.content, [current_field]);
//Here it is used for an object or a property inside an object
```

## 4. UseDebounce Callback
```
function useDebouncedCallback(callback: (...args) => void, wait: number) {
  // track args & timeout handle between calls
  const argsRef = useRef();
  const timeout = useRef();

  function cleanup() {
    if (timeout.current) {
      clearTimeout(timeout.current);
    }
  }

  // make sure our timeout gets cleared if
  // our consuming component gets unmounted
  useEffect(() => cleanup, []);

  return function debouncedCallback(...args: A) {
    // capture latest args
    argsRef.current = args;

    // clear debounce timer
    cleanup();

    // start waiting again
    timeout.current = setTimeout(() => {
      if (argsRef.current) {
        callback(...argsRef.current);
      }
    }, wait);
  };
}
```
Example Usage
```
  const onSearch = useDebouncedCallback(async search_string => {
    if (search_string && search_string.length > 1) {
      setFetching(true);
      const [error, response] = await asyncWrap(searchUsers(search_string));
      setFetching(false);
      if (!error) {
        setUsers(response.data.data);
      }
    }
  }, 300);
```

## 5. useKeyDown With Callback Handler
```
export function useKeydown(key: string, handler: Function) {
  React.useEffect(() => {
    const cb = (e: KeyboardEvent) => e.key === key && handler(e)
    document.body.addEventListener("keydown", cb)
    return () => {
      document.body.removeEventListener("keydown", cb)
    }
  }, [key, handler])
}

```

## 6. UseKeyDownEnhanced with Callback Handler
```
  export function useKeydown(key: string, handler: Function, {   altKey = false, ctrlKey = false, shiftKey = false } ) {
  React.useEffect(() => {
    const cb = (e: KeyboardEvent) => e.key === key  &&
        e.ctrlKey === ctrlKey &&
        e.altKey === altKey &&
        e.shiftKey === shiftKey && handler(e)
    document.body.addEventListener("keydown", cb)
    return () => {
      document.body.removeEventListener("keydown", cb)
    }
  }, [key, handler])
}

```

