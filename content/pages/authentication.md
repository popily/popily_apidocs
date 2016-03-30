Title: Authentication
URL: 
sortorder: 25

Requests to Popily must be authenticated with an API key. You have an API key associated with your account, and we'll also auto-generate one for any User objects you create in our system. This way you can limit access to data on a per-user basis and manage permissions in whatever makes sense to your application.

Technically, this token is passed to Popily as an `Authorization` HTTP header. Like this:

```
Authorization: abc123
```

Where "abc123" is your token. However in practice you'll probably use one the API client libraries. Here's how it works in Python...

```python
from popily_api import Popily
popily = Popily('YOUR API KEY')
```

...and JavaScript.

```javascript
// Node.js
var popily = require('popily')('YOUR API TOKEN');

// With the browser client
popily = popily.api.setToken('YOUR API TOKEN');
```

Keep in mind that **an API key is like a password**, so keep it yourself.