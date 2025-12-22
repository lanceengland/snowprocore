# Account Access and Security

## Outline security principles

### Network security and policies

Network policies in Snowflake are a crucial security feature that allows administrators to control and restrict access to their Snowflake account based on the user's IP address. You can create a network policy that specifies a list of allowed or blocked IP addresses. This means you can ensure that users can only connect to your Snowflake account from trusted locations, such as your corporate network or a specific VPN. This is a powerful tool for preventing unauthorized access from unknown or malicious IP addresses.

### Multi-Factor Authentication (MFA) enforcement

Multi-Factor Authentication (MFA) adds an extra layer of security to your Snowflake account by requiring users to provide a second form of authentication in addition to their password. Snowflake's MFA uses the Duo Security service, which can be easily enabled for individual users or enforced for the entire account. When MFA is enabled, users will need to use an authenticator app on their smartphone to generate a one-time code to complete their login. Enforcing MFA is a highly recommended best practice for protecting your account from unauthorized access, even if a user's password is compromised.

### Federated authentication

Federated authentication allows you to use an external, SAML 2.0-compliant identity provider (IdP) to authenticate your users to Snowflake. This means that your users don't need to have a separate password for Snowflake. Instead, they can log in to Snowflake using their existing credentials from your corporate identity provider, such as Okta, Azure Active Directory, or Ping Identity. This simplifies user management and improves security by centralizing authentication through your existing IdP.

### Key pair authentication

Key pair authentication is a more secure alternative to username/password authentication for programmatic access to Snowflake, such as through the Snowflake drivers or connectors. It uses a public-private key pair, where the public key is assigned to a Snowflake user, and the private key is used by the client to sign a JSON Web Token (JWT) for authentication. This method is more secure because the private key never leaves the client machine, and it eliminates the need to store and manage passwords in your applications.

### Single Sign-On (SSO)

Single Sign-On (SSO) is a feature that allows users to authenticate once with their identity provider and then access multiple applications, including Snowflake, without having to log in to each one separately. Snowflake's support for federated authentication enables SSO. When a user tries to log in to Snowflake, they are redirected to the identity provider's login page. After they successfully authenticate with the IdP, they are automatically logged in to Snowflake. This provides a seamless and secure login experience for your users.

## Define the entities and roles that are used in Snowflake

### Overview of access control

Snowflake's access control model is a powerful and flexible system that combines two key frameworks:

- **Discretionary Access Control (DAC):** In this model, each object has an owner, and the owner can grant access to their objects to other users or roles.
- **Role-Based Access Control (RBAC):** In this model, access privileges are assigned to roles, and roles are then granted to users. A user's access rights are the sum of the privileges of all the roles they have been granted.

#### Access control privileges

A privilege is a defined level of access to a specific object in Snowflake. Privileges can be granted on various objects like databases, schemas, tables, views, and warehouses. Some of the most common privileges include:

- `USAGE`: The ability to use an object, like a warehouse or a database.
- `SELECT`: The ability to query a table or view.
- `INSERT`, `UPDATE`, `DELETE`: The ability to perform DML operations on a table.
- `CREATE`: The ability to create new objects in a schema or database.

### Outline how privileges can be granted and revoked

Privileges are managed using the `GRANT` and `REVOKE` commands in SQL.

- **`GRANT <privileges> ON <object_type> <object_name> TO <role_name>`:** This command is used to grant one or more privileges on an object to a role.
- **`REVOKE <privileges> ON <object_type> <object_name> FROM <role_name>`:** This command is used to revoke privileges from a role.

It's a best practice to grant privileges to roles rather than directly to users.

### Explain role hierarchy and privilege inheritance

Snowflake allows you to create a hierarchy of roles, where you can grant one role to another role. For example, you could have a `developer` role and an `analyst` role, and then you could grant both of these roles to a `data_engineer` role. In this case, the `data_engineer` role would inherit all the privileges of both the `developer` and `analyst` roles. This role hierarchy allows you to create a flexible and scalable access control model that mirrors your organization's structure.

## Outline data governance capabilities in Snowflake

### Accounts

A Snowflake account is the top-level entity in Snowflake, representing a single, isolated environment for your organization. All of your data, users, and objects are contained within your account. From a governance perspective, the account is where you configure account-level settings, such as security policies and resource monitors.

### Organizations

The organization feature in Snowflake allows you to manage multiple Snowflake accounts from a single place. If your company has multiple Snowflake accounts (e.g., for different departments or environments), you can link them together into an organization. This enables you to have a single organization administrator who can monitor usage and manage billing for all the accounts in the organization.

### Secure views

As mentioned before, secure views are a powerful data governance tool. They allow you to share data with other users without exposing the underlying table structure or the logic of the view's query. This is particularly useful when you need to provide access to a subset of your data while protecting sensitive information.

### Secure functions

Similar to secure views, secure UDFs (User-Defined Functions) are a governance feature that prevents users from seeing the underlying logic of the function. This is important when the function contains sensitive information or business logic that you don't want to expose.

### Information schemas

Every database in Snowflake has an `INFORMATION_SCHEMA` that contains metadata about the objects in that database. You can query the views in the `INFORMATION_SCHEMA` to get information about tables, columns, views, procedures, and more. This metadata is essential for data governance, as it allows you to audit your database objects and understand your data landscape.

### Access history

Snowflake's access history provides a detailed log of all the queries that have been executed in your account. This is a critical feature for data governance and auditing.

#### Tracking read/write operations

You can use the `ACCESS_HISTORY` view in the `ACCOUNT_USAGE` schema to track which users have accessed which tables and when. This allows you to answer questions like "Who has queried the `employees` table in the last 30 days?". This level of visibility is essential for data auditing and for understanding how your data is being used.

### Overview of row/column-level security

Snowflake provides powerful features for implementing row-level and column-level security:

- **Row Access Policies:** These policies allow you to control which rows a user can see in a table based on their role. You can create a policy that, for example, only allows sales representatives to see the data for their own region.
- **Masking Policies:** These policies allow you to mask sensitive data in a column based on the user's role. For example, you could create a policy that shows the full social security number to an HR manager, but only shows the last four digits to a financial analyst.

### Object tags

Object tagging is a feature that allows you to attach key-value pairs (tags) to your Snowflake objects, such as tables, columns, and warehouses. You can use tags to classify your data, for example, by tagging columns that contain PII (Personally Identifiable Information). Once you have tagged your objects, you can then use the tags to track sensitive data, apply masking policies, and monitor costs.
