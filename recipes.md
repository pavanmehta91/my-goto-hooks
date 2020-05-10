## 1. Cancelling/Ignoring the data fetched data from older api calls. This helps manage race condition.
For example we've a typeahead UI control and we're making api calls on each keystroke. API all made for earlier invocation may return after the newer invocation. This can result in overriding of data.
```javascript
useEffect(() => {
    if (state.status === "loading") {
      let canceled = false;

      fetchRandomDog()
        .then(data => {
          if (canceled) return;
          dispatch({ type: "RESOLVE", data });
        })
        .catch(error => {
          if (canceled) return;
          dispatch({ type: "REJECT", error });
        });

      return () => {
        canceled = true;
      };
    }
  }, [state.status]);
```
