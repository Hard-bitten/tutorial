Stores two types of data: CooperativeAction and PredictState.

It has basic and self-explainable get methods by name such as:

```cpp
Const CooperativeAction & action() const // Returns action
Const PredictState & state() const // Returns the predicted state
Const boost::shared_ptr <const CooperativeAction> & actionPtr() const // return action pointer
Const boost::shared_ptr <const PredictState> & statePtr() const // returns the state pointer
```