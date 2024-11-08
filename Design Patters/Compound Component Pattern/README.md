# Compound Component Pattern

> References:
>> https://www.patterns.dev/react/compound-pattern/
>
> This document was produced during my personal development day at Voneus

In our application, we often have components that belong to each other. They’re dependent on each other through the shared state, and share logic together.

The **compound component pattern** allows you to create components that all work together to perform a task.

Let's see at this example
```javascript
//Input.js
export function Input({ label, icon, ...props }) {
    return (
        <div className="flex flex-col gap4">
            <label>{label}</label>
            <div className="flex w-full gap-2">
                <input {...props} />
            </div>
        </div>
    )
}
```

In order to apply the pattern, we need to understand the component and separate it into compositions.

> Component
>> Label
>
>> Input
>
>>> Icon

Separating each part we will have something like

```javascript
//Input.js
function Container({ children }) {
    return (
        <div className="flex flex-col gap4">
            { children }
        </div>        
    )
}

function Label({label}) {
    return (
        <label>{label}</label>
    )
}

function InputField(props) {
    return (
        <div className="flex w-full gap-2">
            <input {...props} />
        </div>
    )
}

export Input = {
    Container,
    Label,
    Input: InputField
}
```

New we can mount the input component, let's do variations of the same component

```javascript
//InputText.js
import { Input } from './Input';

export function InputText() {
    return (
        <Input.Container>
            <Input.Label>Input Text Label</Input.Label>
            <Input.Input name="name" />
        </Input.Container>
    )
}
```

Now I hope you're clearer about the possibilities. Following the pattern gives us more flexibility in the components, as well as maintaining the principle of single responsibility and Open/Close Principle for each part of the component.

Some of the beneficial of this pattern are:

• Reusability: By breaking down components into smaller pieces, we can reuse each part across different contexts.

• Flexibility: Allows for custom composition, enabling variations of the same base component.

• Improved Maintainability: Each sub-component handles its own logic, making the overall component easier to maintain and extend.