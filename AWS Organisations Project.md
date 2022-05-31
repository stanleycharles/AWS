# AWS Organisations Project

## How to create a cloud organisation architecture for an entreprise on AWS

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisations%20Diagram.png)

### Scope
- ``Mobby Entreprise`` needs to centralize all his AWS accounts with ``AWS Organisations``.

The `GENERAL` account will become the `MASTER` account for the organisation

We will **invite** The `HR` account as MEMBER accounts and **create** the `Development` account as a MEMBER account.

Before that, we will need the email address of ``the Master Account`` (who also name as the billing master)
 - mobbyentreprise+masterroot@gmail.com (master account)

And 3 seperate emails of different existing AWS accounts in order to associate them to the AWS Organisations.
 - mobbyentreprise+hr@gmail.com (human ressources)
 - mobbyentreprise+dev@gmail.com (development)
 - mobbyentreprise+fin@gmail.com (finance)

The ``Master Account`` will create and be convert to the ``Management Account`` of this AWS Organisation.

To integrate The ``HR`` account into the organisation, the Master account will sent a ``verification email`` to them. 

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Invitation.png)

After the validation, ``HR`` will integrate the organisation.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Accounts.png)

Now, we will see how we can role-switch into this ``HR`` AWS account witch is now **a member** of the organisation.

We will also create an `OrganizationAccountAccessRole` in the `Master Account`, and use this ``role`` to switch between accounts.

The role `OrganizationAccountAccessRole` will get the **administrator access** to everything. After creating this role, clic on `Switch role`.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Switch-Back.png)

And fill out all the cases: The Account ``HR`` or ``Finance`` ID number, a role and a random display name.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Create%20Switch-Role-HR.png) 

It's going to create an entry within the console GUI and this is stored in your browser.
This is just creating ``a shortcut`` so you can easily access in the future. And it's actually using the `OrganizationAccountAccessRole` API Call to assume the role that we just created aka the ``Administrator Access`` role.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Switch-Role-HR.jpg)


**Last thing**. To integrate the ``Development`` account into the organisation, we will create a **brand new** account directly with AWS Organisations. For this time, we won't invite an existing AWS account like we did before with `HR`.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Create%20AWS%20Organisations%20-%20New%20Account-Dev.png)

Now, the organisation have ``3`` accounts easily ``switchable`` if you already did the same adding role process to the ``Development`` account into the organisation.

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Create%20Switch-Role-Dev.png)

![This is an image](https://github.com/stanleycharles/AWS/blob/main/AWS%20Organisations%20Project/AWS%20Organisation%20-%20Switch-Role-Dev.png)


  ---
  
  ## Ressources
   - https://docs.aws.amazon.com/organizations/latest/userguide/orgs_getting-started_concepts.html
   - https://cantrill.io/
   
