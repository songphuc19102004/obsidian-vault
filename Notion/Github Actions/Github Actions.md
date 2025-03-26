> What is Github Actions?

→ A Workflow Automation Service by Github. This service allows us to automate all kinds of repositories-related processes and actions. It offers various automations around the code that is stored in repositories. CI/CD is more used.

![[/image 138.png|image 138.png]]

![[/image 1 2.png|image 1 2.png]]

  

# Basic Building Blocks & Components

## Key components

### Workflows, Jobs, and Steps

==**Workflows**== are ==**attached**== to a specific Github ==**Repo**==. We can add workflows to Github Repo ==**as many as we want**==. It is kind of the ==**first thing**== we build when setting up an automation process with Github Actions.

  

A Workflow includes ==**one or more**== ==**Jobs**==.

  

A Job contains ==one or more== ==**steps**== that will be executed in the order which they’re specified. It defines what the actual thing that will be done. ==Like download the code in the first step, install dependencies on the second step, and run automated tests in the third step.==