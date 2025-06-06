# pytorch
## gradiant disappearance (all be zero)
### backgroud
Parameters of a module training cannot step forward anymore after some forward calculation.
### trouble shooting
Add the following lines to the training methods before doing forward processing.
```
def backward_hook(module, grad_input, grad_output):
    print("-------------------------------- backward hook --------------------------------")
    print(f"{module.__class__.__name__}")
    print(f"grad_input: {grad_input}, grad_output: {grad_output}")
for name, param in module.named_parameters():
    print(f"param {name} grads: {param.grad}")
for name, module in self.v.named_modules():
    module.register_full_backward_hook(backward_hook)
```
And got the print of gradients during the backward propergation. It shows that the
gradients all being **0**

Then check the network structure, added logs to print the result of activiation function (which is sigmoid).
And got the result of sigmoid is **0** (or **1**)

According to the gradient calculation of sigmoid:
```
grad = y * (1 - y)
```
it's obvious that the sigmoid is the reason which caused the disappearance of gradients.
