# dim-element
[lit-element](https://lit-element.polymer-project.org)'s less bright little brother.

**dim-element is highly experimental! Use at your own peril!**

lit-html and lit-element are fantastic for handling dynamic templates. But what if your templates are mostly static? Can you get some of the benefits of lit-element without the (relatively small) dependency on lit-element?

Enter dim-element! dim takes advantage of lit-element's UpdatingElement to handle properties and lifecycle, but it uses simple strings instead of lit-html to build your element's Shadow DOM. Instead of optmizing for fast, efficient updates, dim optimizes for quick startups and renders, at the expense of making re-renders much slower and less effective.

## When you should you ~~be~~ use dim instead of lit?

Sometimes, your template doesn't change. If that's the case, dim is your tool!

You may see this often in elements that are part of design systems, where some components are almost completely static and mostly encapsulate styling and maybe some Javascript behaviors, none of which modify the template directly. This is the use case that prompted the creation of dim - when rebuilding [byu-theme-components](https://github.com/byuweb/byu-theme-components) to use lit-element, we realized two things: 1) we didn't use lit's capabilities very much, and 2) lit-element and lit-html comprised about 50% of our bundle size.

## When shouldn't you use it?

Any time you need your template to change over the lifetime of an element. If you're doing any dynamic templating at all, you should stick with something that's better at updating, like lit-element.

# Example

```ts
    import {DimElement, css, customElement, listen, property} from 'dim-element';

    // You can use most decorators from lit-element
    @customElement('my-element')
    export class MyElement extends DimElement {

      // You can declare properties in exactly the same way as with lit-element
      @property()
      clicked = false;

      // instead of having a dynamic template, we just change attributes and let CSS handle the rest
      static styles = css`
        :host[clicked] {
          background-color: blue;
        }
        span {
          color: green;
        }`;

      // Render element DOM by returning a simple string template.
      render() {
        return `Web Components are <button id="great">great</button>!`;
      }
      
      // Set up a listener on an element in the template. Almost, but not really, makes up for not having
      // lit-html's `@event` syntax.
      @listen({to: 'click', on: '#great'})
      onGreatClick(e) {
        this.clicked = true;
      }

    }
```
