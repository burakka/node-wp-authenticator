# WP Auth (node-wp-authenticator)

![npm](https://img.shields.io/npm/v/wp-auth)
![license](https://img.shields.io/npm/l/wp-auth)

A Node.js package to authenticate users using WordPress cookies.

## Installation

Install via npm:

```bash
npm install wp-auth
```

## Usage

### 1. Initialization

Initialize the authenticator with your WordPress database and keys.

```javascript
const wp_auth = require('wp-auth').create({
    wpurl: 'http://my-blog.example',
    logged_in_key: 'LOGGED_IN_KEY from wp-config.php',
    logged_in_salt: 'LOGGED_IN_SALT from wp-config.php',
    mysql_host: 'localhost',
    mysql_user: 'root',
    mysql_pass: 'password',
    mysql_port: 3306, // Optional, defaults to 3306
    mysql_db: 'wordpress_db',
    wp_table_prefix: 'wp_', // Optional, defaults to 'wp_'
    skipAuthentication: false, // Optional, defaults to false
    debug: false, // Optional, defaults to false
    maxRetries: 5, // Optional, defaults to 5
    cacheTimeout: 300000 // Optional, defaults to 5 minutes (in ms)
});
```

#### Configuration Options

| Option | Type | Description |
| :--- | :--- | :--- |
| `wpurl` | String | The URL of your WordPress site. |
| `logged_in_key` | String | The `LOGGED_IN_KEY` constant from your `wp-config.php`. |
| `logged_in_salt` | String | The `LOGGED_IN_SALT` constant from your `wp-config.php`. |
| `mysql_host` | String | MySQL database host. |
| `mysql_user` | String | MySQL database username. |
| `mysql_pass` | String | MySQL database password. |
| `mysql_db` | String | MySQL database name. |
| `mysql_port` | Number | MySQL port (default: `3306`). |
| `wp_table_prefix` | String | WordPress table prefix (default: `wp_`). |
| `skipAuthentication` | Boolean | If `true`, bypasses hash checking and trusts the cookie ID (use with caution, mostly for testing). |
| `debug` | Boolean | If `true`, enables verbose console logging. |
| `maxRetries` | Number | Number of connection retries if MySQL connection fails. |
| `cacheTimeout` | Number | Time in milliseconds to cache meta values and authentication checks (default: `300000` ms / 5 mins). |

---

### 2. Check Authentication

Verify if a user is logged in based on the request's cookies.

```javascript
wp_auth.checkAuth(req).on('auth', function(auth_is_valid, user_id) {
    if (auth_is_valid) {
        console.log(`User is logged in. ID: ${user_id}`);
    } else {
        console.log('User is not logged in.');
    }
});
```

- **`req`**: The HTTP request object (must have `headers.cookie`).
- **`auth_is_valid`**: `true` if logged in, `false` otherwise.
- **`user_id`**: The WordPress User ID (or `0` if not logged in).

---

### 3. User Meta Operations

#### Get User Meta

Retrieve a specific meta value for a user.

```javascript
wp_auth.getUserMeta(user_id, 'first_name', function(data) {
    console.log('First Name:', data); 
    // data is null if key/user doesn't exist
});
```

#### Get Multiple User Metas

Retrieve multiple meta values at once.

```javascript
wp_auth.getUserMetas(user_id, ['first_name', 'last_name'], function(data) {
    console.log('User Data:', data);
    // Returns array: [{ first_name: 'John' }, { last_name: 'Doe' }]
});
```

#### Set User Meta

Update or create a meta value for a user.

```javascript
wp_auth.setUserMeta(user_id, 'user_status', 'active');
```

#### Reverse User Meta Query

Find a user ID based on a specific meta key-value pair.

```javascript
wp_auth.reverseUserMeta('nickname', 'SuperUser', function(id) {
    if (id) {
        console.log(`Found User ID: ${id}`);
    } else {
        console.log('No user found.');
    }
});
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
