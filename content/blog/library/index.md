---
title: Develop the right Table component
date: '2019-01-13'
---

### Table component
Table component is a component that showed data in tabular-way. 
The criteria's of the table component is
- reusable
- customizable 
- extendable

### Reusable
It's very important to create reusable component that strictly follow the rules
- component have to be isolated
- component to be free from specificity

#### isolated
The objective to provide isolation is to ensure component developed didn't override other components
and it's scope never exposed to the outsider and prevent from be overrided.

#### specificity
Abusing code with specificity always exposing coding into bespoke style. 
And the JavaScript is poor on supporting bespoke style as it's not strong type language by default.

Here is simple illustration:

```js
 if (invoker === 'app1') {
     return (<table className='app1' />)
 } else if (invoker === 'app2') {
     return (<table className='app2' />)
 } else if ...
```

### Customizable
Often Ui developer choose to develop configurable Ui in order to support customization. I'm one of them. This is sample illustration the idea of configurable Ui:

```js
const table = new ConfigUi({
    type: 'multiSelect',
    theme: 'dark',
    items: [
        { type: 'row', items: [ { type: 'col', value: 'Td' } ] }
    ]
    ...
})
```

However above pattern not really suit for React/JSX that aim to cater on the markup instead.
Then, what is the suitable pattern for configurable Ui in React/JSX?

Let's try to convert above code into JSX:

```jsx
<TableComponent multiSelect dark>
    <TableComponentRow>
        <TableComponentCell>
            ...
        </TableComponentCell>
    </TableComponentRow>
</TableComponent>
```

> TableComponent

```js
const { children } = this.props;
return (
    <Table>
        {children}
    </Table>
)
```

> TableComponentRow

```js
const { children } = this.props;
return (
    <tr>
        {children}
    </tr>
)
```

> TableComponentComponent

```js
const { children } = this.props;
return (
    <td>
        {children}
    </td>
)
```

Everything look good BUT above JSX pattern has one challenge we need to deal with.
I will cover the challenge and solution after we done with "extendable" part.

### Extendable
In Human JavaScript book, wrote by the author of the phenomenal backbone framework called Ampersand --Henrik Joreteg

Henrik give the best reason to uncoupled component with the app that invoking it:

> it's very easy to fall into the trap of tightly coupling your application to 
> that particular client.This makes it much harder to build other clients, 
> say, for example, an iOS app for your app. 
> While these frameworks are useful for standard desktop web apps, they let us down a bit 
> when we want to go beyond that. With more and more talk of "the Internet of things" 
> we have good reason to believe that the breadth of device types that want 
> to talk to your app will continue to increase

One way to uncoupled the component with the app is to provide a way for the app to extendable the component. Here is illustration on providing extendable component.

```js
const NewTableComponent = Extend(TableComponent, {
    items: [...],
    theme: `
        table {
            border: 2px solid ${colorTheme}
        }
    `;
})
```

As promise, we will go thru on the challenge when we apply configurable pattern on the markup - JSX
The main challenge is how manage communication between TableComponent and it's nested components.

Let take code as sample:

```jsx
<TableComponent striped>
    <TableComponentRow>
        <TableComponentCell>
            ...
        </TableComponentCell>
    </TableComponentRow>
</TableComponent>
```

Here is requirements:

- when striped prop added, row:even showed dark background color 
- while row:odd should light background color
- by default table rows showed white background color

![w3school table sample](./striped-table.png)

We can easily tackle this by passing the prop "striped" to the TableComponentRow. However it is not always the "easy" thing is the "smart" thing, especially on this case. It's ugly, i must admit it. No, WE must admit it.

```jsx
<TableComponent striped>
    <TableComponentRow striped>
        <TableComponentCell>
            ...
        </TableComponentCell>
    </TableComponentRow>
</TableComponent>
```

You must think why we are not explicitly add the striped prop in component script instead.
But remember, in the script the row is table {children} which table doesn't know and care about it.

```js
return (
    <table>
        {children}
    </table>
)
```

Worry not, in the latest React, the team introduce one super-awesome pattern called Context API.

--to be continue