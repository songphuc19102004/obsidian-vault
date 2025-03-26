[[View]]

  

  

  

  

  

  

  

![[/Untitled 142.png|Untitled 142.png]]

> [!important] Model-View-Controller (MVC) is an architectural pattern that separates application code into three main components: Models, Views, and Controllers

- Controller can invoke everything:
    - Controller can invoke Model (1)
    - Controller can invoke View (2)
- View can invoke the Model (3)

  

There are 2 types of models:

- Business model and View model

  

![[/Untitled 1 26.png|Untitled 1 26.png]]

1. Controller invokes Business Model.
2. Controller creates object of View Model.
3. Controller invokes View.
4. View accesses View Model.

  

### Responsibilities of Model-View-Controller

**Controller**

- Receives HTTP request data.
- Invoke business model to execute business logic.

**Business Model**

- Receives input data from the controller.
- Performs business operations such as retrieving / inserting data from database.
- Sends data of the database back to the controller.

**Controller**

- Creates object of ViewModel and files data into its properties.
- Selects a view & invokes it & also passes the object of ViewModel to the view.

**View**

- Receives the object of ViewModel from the controller.
- Accesses properties of ViewModel to render data in html code.
- After the view renders, the rendered view result will be sent as response.

  

### Benefits / Goals of MVC architectural pattern

- Clean separation of concerns
- Each component (model, view and controller) performs single responsibility.
- Identifying and fixing errors will be easy.
- Each component (model, view and controller) can be developed independently.
- In practical, both view and controller depend on the model.
- Model doesn't depend on neither view nor the controller.
- This is one of the key benefits of the 'clean separation'.
- This separation allows the model to be built and tested independently.
- ==**Unit testing**== each individual component is easier.