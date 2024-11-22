# S.O.L.D

> References:
>> https://www.ramotion.com/blog/frontend-design-patterns/
>
> This document was produced during my personal development day at Voneus

Solid is a widely known and commonly used principle in object-oriented programming. However, below we will explore its use in front-end development.

[Single Responsibility](#Single-Responsibility): Each software module should have one and only one reason to change.

[Open Closed](#Open-Closed): A module should be open for extension but closed for modification.

[Liskov Substitution](#Liskov-Substitution): Subclasses should be substitutable for their base classes.

[Interface Segregation](#Interface-Segregation): Many client specific interfaces are better than one general purpose interface.

[Dependency Inversion](#Dependency-Inversion): Depend upon Abstractions. Do not depend upon concretions.


## Examples

### Single Responsibility

As you can see, the component's only responsibility is to return the styled HTML element.

```javascript
// Input.js
export function Input(props) {
    return (
        <input className="w-full border bg-transparent" {...props} />
    )
}
```

```javascript
// LabelledComponent.js
export function LabelledComponent({label, children}) {
    return (
        <div className="flex flex-col gap4">
            <label>{label}</label>
            {children}
        </div>
    )
}
```

```javascript
// LabelledInput.js
import { LabelledComponent } from './LabelledComponent.js';
import { Input } from './Input';

export function LabelledInput({label, ...props}) {
    return (
        <LabelledComponent label={label}>
            <Input {...props}>
        </LabelledComponent>
    )
}
```

### Open Closed

Our component should be ready for the extension without having to be modified, in the example below we will import the component input by converting it into an input search.

```javascript
// InputSearch.js
import { LabelledComponent } from './LabelledComponent.js';
import { Input } from './Input';

export function InputSearch({label, ...props}) {

    function handleChange(event) {
        console.log('Searching:', event.target.value);
    }


    return (
        <LabelledComponent label={label}>
            <div className="w-full flex gap-2">
                <Input placeholder="search..." onChange={handleChange} {...props} />
                <img src="https://static.thenounproject.com/png/950829-200.png" alt="search icon" className="w-5 h-5" />
            </div>
        </LabelledComponent>
    )
}
```

### Liskov Substitution

Once we have the base element and the extended element, we can use both independently.

```javascript
//FormRegister.js
import { LabelledInput } from './LabelledInput';
import { InputSearch } from './InputSearch';

export function FormRegister() {
  return (
    <form>
      <h1>Form Example</h1>

      <LabelledInput name="name" label="Enter your name" />

      <InputSearch name="postcode" label="search the postcode" />
    </form>
  );
}
```

### Interface Segregation

Clients should not be forced to depend on interfaces they do not use.

Each input component uses a specific interface (props) with only the necessary properties.

```javascript
// InputNumber.js
import { LabelledInput } from './LabelledInput';

export function InputNumber({type, ...props}) {
    return (
        <LabelledInput type="number" {...props} />
    )
}
```

```javascript
// InputCheckbox.js
import { LabelledInput } from './LabelledInput';

export function InputCheckbox({type, checked, ...props}) {
    return (
        <LabelledInput type="checkbox" checked={checked} {...props} />
    )
}
```

### Dependency Inversion

Imagine you have a form that uses various input components like TextInput, NumberInput, and CheckboxInput. Instead of hardcoding specific input components directly into your form, you can create an abstraction for input components. This allows the form to depend on an abstraction rather than concrete implementations, making it easier to switch out or extend input types without modifying the form logic.

```javascript
// DynamicForm.js
import { LabelledInput } from './LabelledInput';
import { InputNumber } from './InputNumber';
import { InputCheckbox } from './InputCheckbox';

/** Inputs: [Array]
 * type: TEXT | NUMBER | CHECKBOX
 * props: input props
**/
export function DynamicForm({inputs, ...props}) {

    function DynamicInput({ type, ...props}) {
        switch (type) {
            case 'NUMBER':
                return <InputNumber {...props} />
            case 'CHECKBOX':
                return <InputCheckbox {...props} />
            default:
                return <LabelledInput {...props} />
        }
    }

    return (
        <form {...props}>
            {inputs.map(({id, ...inputProps}) => <DynamicInput key={id} id={id} {...inputProps} />)}
        </form>
    )
}
```

Now implementing the dynamic form on a page.

```javascript
//Page.js
import { DynamicForm } from './DynamicForm';

export function Page() {

    const formInputs = [
        { type: 'TEXT', id: 'name', label: 'Your name', name: 'name'},
        { type: 'NUMBER', id: 'age', label: 'Your age', name: 'age'}
    ]

    return (
        <div className="p-4">
            <h1>Register with Dynamic Form</h1>
            <DynamicForm inputs={formInput} />
        </div>
    )
}
```

As you can see, SOLID implementation is simple when you think about it from the ground up. By following these principles, the application will become increasingly secure, scalable and maintainable.

These principles enhance the scalability, maintainability, and testability of your React application, paving the way for a robust and clean codebase.