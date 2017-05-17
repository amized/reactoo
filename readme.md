# Roo

### Stateful objects that will update your React components.

## Object Oriented programing
Let's say you want to use plain javascript classes to describe the logic and state of your application. You want your objects to control their own state and logic for how that state can be manipulated. 
Roo gives you a way of doing this while using React for your UI.

## Who's this really good for?

In some sense React already adopts an OO methodology - components are objects that hold state and logic for updating their state. And this is great for describing a user interface. It's even great for building simple applications who's job it is to display or submit data, like a website.

But let's say you're building a more complex web/HTML application like...

* a game
* a simulation
* an AI
* modeling

Performance may be a critical issue, and you'll probably want to structure your application logic yourself, and make sure it is decoupled from your UI. Javascript classes are a great way of modularising your logic, but currently it's a bit messy trying to mix these with React.

## Example
Let's say your application has an Organisation.



	
```javascript
class Organisation {
  setName(name) {
    this.name = name
  }
}
```
	
And then you use React to render your UI by passing in your object as a prop:

```javascript
let myorg = new Organisation()

<MyComponent org={myorg} />	
```

For the initial render, this works ok. But if I want to my member function ```setName()``` to trigger an update on my react component, we have to do something like this:

```javascript

class MyWrapper extends React.Component {
  
  constructor() {
    super(props);
    this.state = {
  	   org: new Organisation()
  	 }
  }

  setOrgName = () => {
    this.state.org.setName("bar")
    this.setState({
      org: this.state.org
    })
  }
  
  render() {
    return (
      <div>
        <MyComponent org={this.state.myorg} setOrgName={this.setOrgName}/>
      </div>
    )
  }
}



class MyComponent extends React.Component {

  render() {
    return (
      <div>
        <div>{this.props.org.name}</div>
        <button onClick={this.props.setOrgName}>Click</button>
      </div>
    )
  }

}
```
### This is problematic for a few reasons

* I need to manually keep track every time I call member functions on ```Organisation``` that modify the object's state, and then make sure I follow it with a ```setState()```


* If I call the member function from outside React, or from a different component, there is no way to update the associated React elements on the page

* Code is bloated and ugly!

## How Roo works
Let's solve this by adding a ```@stateChange``` decorator to our function:

```javascript
import { stateChange } from 'roo-react'

class Organisation extends Class  {

  @stateChange
  setName(name) { 
    this.name = name; 
  }
  
}
```	

What the ```@stateChange``` decorator does is tells Roo that the code in your function will in some way modify the state of your object, and therefor it should update the relevant React components to reflect the change your UI.

To get this to work with your components use the ```connect``` wrapper:

```javascript
import { connect } from 'roo-react'

class MyComponent extends React.Component {
  render() {
    const org = this.props.org
    return (
      <div>
        <div>{org.name}</div>
        <button onClick={()=>{ org.setName("bar") }}>Click</button>
      </div>
    )
  }
}
	
MyComponent = connect(MyComponent)
```
Notice now there is no need for a ```setState()``` call, and no need to put state-modifying logic into the component. Also conviniently, the methods for updating the state are passed along with the objects, saving code on explicitly passing down state-setting functions through props.

Don't forget to pass in your object as a prop to the component to bind your components to the object

```javascript
let myorg = new Organisation()
...
<MyComponent org={myorg} /> 
```

And that's it!

I can now call the member function on my object from anywhere in my application and the React element will appropriately update.

```javascript
$(".IJustWannaUseJqueryHereOk").on("click", ()=>{
  myorg.setName("James")
  // The React component updates
}	

```


Feedback is welcome.

 

## License

MIT | © 2017 [Amiel Zwier](http://amielzwier.com)
	