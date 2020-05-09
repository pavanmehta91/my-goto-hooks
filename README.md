# my-goto-hooks
custom hooks, self-built, copied or inspired But useful nonetheless
## KeyPress Hook which returns a true flag
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
