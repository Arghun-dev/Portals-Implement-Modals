# Portals-and useRef for Implement Modals

Create a div with an id of `modal` on top of react div with the id of the react root div. and leave it empty.

Now, we're going to create a React component that only renders into this one. (The div with the id of modal). And then, now it's above our application entirely and we can do whatever we want.


**ref**

A ref is something like I have one value that I need to refer to the exact same thing across all renders. Whereas like `useState` you get the current state which you can blow away and recreate different renderings have access to different versions of the same state. With ref, you're saying across all of this rendering, I wanna refer to exactly the same thing. Precisely the same thing.

```js
import { useEffect, useRef } from "react";
import { createPortal } from "react-dom";

const Modal = ({ children }) => {
  const elRef = useRef(null);
  
  if(!elRef.current) {
    elRef.current = document.createElement("div");
  }
 
  useEffect(() => {
    const modalRoot = document.getElementById("modal");
    modalRoot.appendChild(elRef.current);
  }, [])
}
```

Inside `useEffect` I'm appending new div tag (elRef) inside modal root tag. And we're gonna say only do this at the first render. We don't wanna render into it every single time. We just wanna start that rendering process and then go from there.

And now, here we have a problem with this component, we're creating this div inside useEffect and we don't destroy it. Yeah, if we don't remove this afterwards, because we're doing this like appendChild buisiness, we're gonna leak, because it's gonna keep appending children, it's never going to remove them. React doesn't remove it for us.

So, we have to tell React, when this gets destroyed, or on `componentWillunmount`, the way you do it, it's in useEffect return a function. This function, gets invoked, whenever this modal goes away.

```js
import { useEffect, useRef } from "react";
import { createPortal } from "react-dom";

const Modal = ({ children }) => {
  const elRef = useRef(null);
  
  if(!elRef.current) {
    elRef.current = document.createElement("div");
  }
 
  useEffect(() => {
    const modalRoot = document.getElementById("modal");
    modalRoot.appendChild(elRef.current);
    
    return () => modalRoot.removeChild(elRef.current);
  }, [])
  
  return createPortal(<div>{children}</div>, elRef.current);
};

export default Modal;
```


Modal.tsx

```js
import { useEffect, useRef, FunctionComponent, MutableRefObject } from "react";
import { createPortal } from "react-dom";

const Modal: FunctionComponent = ({ children }) => {
  const elRef: MutableRefObject<HTMLDivElement | null> = useRef(null);
  
  if(!elRef.current) {
    elRef.current = document.createElement("div");
  }
 
  useEffect(() => {
    const modalRoot = document.getElementById("modal");
    if (!modalRoot || !elRef.current) return;
    modalRoot.appendChild(elRef.current);
    
    return () => {
      if (elRef.current) {
        modalRoot.removeChild(elRef.current);
      }
    }
  }, [])
  
  return createPortal(<div>{children}</div>, elRef.current);
};

export default Modal;
```
