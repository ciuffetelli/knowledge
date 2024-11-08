# Provider Pattern

> References:
>> https://www.patterns.dev/vanilla/provider-pattern/
>
> This document was produced during my personal development day at Voneus

Sometimes information needs to be accessed by several components, this is usually done from a parent element to a child element via the props, but the complexity increases when more and more components need to interact with the same information, state.

Provider pattern consists of provide a state globally available to all its child components.

## State sharing by props

```javascript
function FormAdd({ setNames }) {

    const [value, setValue] = useState('');

    const addName = (name) => {
        if(names.includes(name)) {
            return;
        }

        setNames((current) => ([...current, name]));
    }

    const handleClick = () => {
        addName(value);
        setValue('');
    }

    return (
        <div>
            <input name="name" value={value} onChange={(e) => setValue(e.target.value)} />
            <button onClick={handleClick}>Add</button>
        </div>
    )
}

function RemoveButton({ name, setNames }) {

    const handleClick = () => {
        setNames((current) => current.filter((item) => item !== name ));
    }

    return (
        <button onClick={handleClick}>Remove</button>
    )
}

function Component() {

    const [names, setNames] = useState([]);

    return (
        <div>
            <h1>Names</h1>
            {names.map((item,index) => (
                <div key={index}>
                    {item}
                    <RemoveButton name={item} setNames={setNames} />
                </div>
            ))}
            <div>
                <FormAdd setNames={setNames} />
            </div>
        </div>
    )
}
```

In the example we can see that to Add/Remove a nine we are forced to go through the proposals of the state controller, setNames, of the root component. Thinking about it in a broader application, this approach could create:

• Performance loss
• Maintenance difficulties
• Extra complexity

Since each component that depends on this information will have to deal with each other, interacting with each other.

## State sharing by providers

```javascript
function Provider(children) {

    const [names, setNames] = useState([]);

    return (
        <Context.Provider value={{
            names,
            setNames
        }}>
         {children}
        </Context.Provider>
    )
}

function FormAdd() {

    const { setNames } = useProvider();
    const [value, setValue] = useState('');

    const addName = (name) => {
        if(names.includes(name)) {
            return;
        }

        setNames((current) => ([...current, name]));
    }

    const handleClick = () => {
        addName(value);
        setValue('');
    }

    return (
        <div>
            <input name="name" value={value} onChange={(e) => setValue(e.target.value)} />
            <button onClick={handleClick}>Add</button>
        </div>
    )
}

function RemoveButton({ name }) {

    const { setNames } = useProvider();

    const handleClick = () => {
        setNames((current) => current.filter((item) => item !== name ));
    }

    return (
        <button onClick={handleClick}>Remove</button>
    )
}

function Component() {

    const { names } = useProvider();

    return (
        <div>
            <h1>Names</h1>
            {names.map((item,index) => (
                <div key={index}>
                    {item}
                    <RemoveButton name={item} setNames={setNames} />
                </div>
            ))}
            <div>
                <FormAdd setNames={setNames} />
            </div>
        </div>
    )
}

function ComponentWithProvider() {
    return (
        <Provider>
            <Component />
        </Provider>
    )
}
```

Now, through ComponentWithProvider, all child components will have access to the context, which means that they share state with each other.

At first, it generates an extra layer, but this is soon offset by the gains in performance and maintainability.