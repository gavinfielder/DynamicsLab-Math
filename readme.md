# DynamicsLab Math

This is the DynamicsLab math library for numerically solving generalized n-dimensional ODE systems using the Runge-Kutta method of order 4. 

Last updated 5/14/2018

## Dependencies
[B83.ExpressionParser](http://wiki.unity3d.com/index.php/ExpressionParser)

## Namespaces
### DynamicsLab.Vector
dlab-vector.cs holds the two basic structures used in this library:
 - **VectorND** is a generalized n-dimensional numerical vector of underlying type double. It is dimensionalized with a byte, and so can take up to 255 dimensions.
 - **FunctionVectorND** wraps an array of function delegates to emulate an n-dimensional vector function. The delegate type is defined in [B83.ExpressionParser](http://wiki.unity3d.com/index.php/ExpressionParser).
### DynamicsLab.Data
dlab-data.cs holds data storage structures. 
- **DataSet** manages a dynamically growing and shrinking set of generalized n-dimensional solution data. It assumes the data is arranged linearly on an independent variable, referenced by t. It can retrieve values both at a direct index like an array or retrieve the nearest value to a passed double for t. 
### DynamicsLab.Solvers
dlab-ivp.cs holds structures and algorithms for solving ODE systems.
 - **ODEInitialValueProblem** manages an initial value problem. It has functionality for defining the equations and initial conditions and then solving the system to a specified value of the independent variable.
 - **SpringMassSystem** is an example wrapper of ODEInitialValueProblem that uses the math library to solve the basic wall-spring-mass system taught in probably every differential equations class anywhere.
## Usage
Consider the following system:
![enter image description here](https://i.imgur.com/5YmzPZe.png?1)
*(Taken from Numerical Analysis 9th edition by Burden and Faires, page 337)*
 Note h in the above problem refers to data resolution, the interval of time between data points. In the code below, 0.01 is used instead:
```csharp
//Create an ODE initial value problem
ODEInitialValueProblem ivp = new ODEInitialValueProblem(3, 0.01, 0); //3 dimensional, 0.01 resolution, starts at t=0
ExpressionParser parser = new ExpressionParser();
//Note ivp needs the ToDelegate to see "t" followed by all state variables, regardless whether they are used
ivp.F.funcs[0] = parser.EvaluateExpression("u1+2*u2-2*u3+e^(-t)").ToDelegate("t", "u1", "u2", "u3");
ivp.F.funcs[1] = parser.EvaluateExpression("u2+u3-2*e^(-t)").ToDelegate("t", "u1", "u2", "u3");
ivp.F.funcs[2] = parser.EvaluateExpression("u1+2*u2+e^(-t)").ToDelegate("t", "u1", "u2", "u3");
ivp.SetState(new VectorND(3, -1, 1), 0); //initial condition  at t=0
//Solve the problem to t=2.5
ivp.SolveTo(2.5);
//Find the value of u1 at t=0.73
double result1 = ivp.SolutionData(0, 0.73);
//Find the value of u3 at t=1.4
double result2 = ivp.SolutionData(2, 1.4);
```

