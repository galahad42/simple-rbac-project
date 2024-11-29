
# Simple RBAC Project

## **Overview**

This project implements a **Role-Based Access Control (RBAC)** system to secure resources by granting permissions based on user roles. It incorporates core concepts of authentication, authorization, and RBAC to provide fine-grained access control. The system is modular and flexible, making it easy to extend and adapt for real-world applications.

---

## **Features**

1. **Authentication**:
   - Secure user registration and login using **Passport.js**.
   - Passwords are securely hashed before storage.

2. **Authorization**:
   - Access to endpoints is restricted based on user roles.
   - Middleware ensures permissions are validated before resource access.

3. **RBAC**:
   - Configurable roles with associated permissions in `roles.json`.
   - Flexible architecture for defining and managing roles and permissions.

4. **Key Functionalities**:
   - Role-specific access control for resource creation, reading, updating, and deletion.
   - Modular codebase to easily extend roles and permissions.

---

## **System Architecture**

### **1. Role Definitions**
Roles and permissions are defined in the `configs/roles.json` file:
```json
{
  "roles": [
    {
      "name": "admin",
      "permissions": [
        "create_record",
        "read_record",
        "update_record",
        "delete_record"
      ]
    },
    {
      "name": "manager",
      "permissions": [
        "create_record",
        "read_record",
        "update_record"
      ]
    },
    {
      "name": "employee",
      "permissions": [
        "read_record"
      ]
    }
  ]
}
```

### **2. Middleware for RBAC**
The `middlewares/rbacMiddleware.js` contains the logic for checking permissions:
```javascript
exports.checkPermission = (permission) => {
    return (req, res, next) => {
        const userRole = req.user ? req.user.role : 'anonymous';
        const userPermissions = new Permissions().getPermissionsByRoleName(userRole);

        if (userPermissions.includes(permission)) {
            return next();
        } else {
            return res.status(403).json({ error: "Access denied" });
        }
    };
};
```
- It validates whether a user’s role has the required permission before allowing access.

### **3. Models**
- **User Model** (`models/user.js`):
  Defines users with fields for username, password, and role.
  ```javascript
  const userSchema = new mongoose.Schema({
      username: { type: String },
      password: { type: String },
      role: { type: String }
  });
  ```

- **Role Model** (`models/role.js`):
  Manages role definitions using the `roles.json` configuration file.
  ```javascript
  class Role {
      getRoleByName(name) {
          return this.roles.find((role) => role.name === name);
      }
  }
  ```

- **Permissions Model** (`models/permissions.js`):
  Retrieves permissions for a given role.
  ```javascript
  class Permission {
      getPermissionsByRoleName(roleName) {
          const role = roles.roles.find((r) => r.name === roleName);
          return role ? role.permissions : [];
      }
  }
  ```

### **4. Endpoints**
- **Authentication Routes**:
  - `POST /auth/register`: Register a new user.
  - `POST /auth/login`: Authenticate a user.

- **Protected Routes**:
  - `GET /records`: Access records (requires `read_record` permission).
  - `POST /records`: Create a record (requires `create_record` permission).

---

## **Technologies Used**

- **Node.js**: Backend runtime environment.
- **Express.js**: Web framework.
- **MongoDB**: Database for user and role data.
- **Passport.js**: Authentication library.
- **JWT**: For secure session management.

---

## **Setup Instructions**

1. Clone the repository:
   ```bash
   git clone https://github.com/galahad42/simple-rbac-project.git
   ```
2. Navigate to the project directory:
   ```bash
   cd rbac-project
   ```
3. Install dependencies:
   ```bash
   npm install
   ```
4. Configure the `.env` file:
   - Add database URI and secret keys.
   ```env
   MONGO_URI=your-mongodb-connection-string
   JWT_SECRET=your-jwt-secret
   ```
5. Start the server:
   ```bash
   npm start
   ```

---

## **Usage**

1. Register a user and assign a role (`admin`, `manager`, or `employee`).
2. Log in to obtain a JWT token.
3. Use the token to access protected endpoints.

---

## **Security Features**

1. **JWT**: Provides secure, stateless authentication.
2. **RBAC Middleware**: Prevents unauthorized access to endpoints.

---

## **Future Improvements**

1. Implement advanced logging for unauthorized access attempts.
2. Add UI for easier role management.
3. Integrate multi-factor authentication (MFA).

---

This project demonstrates a robust and extensible implementation of RBAC suitable for real-world applications.
