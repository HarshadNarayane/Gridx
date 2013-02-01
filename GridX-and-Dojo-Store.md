GridX has a relationship with Dojo Store.  Since Dojo Store can be implemented by users it is essential that a Dojo Store provide the functions used by GridX.

GridX expects the store to implement:

* `getIdentity(object)` - Return the identity key given an object
* others ...