# Testing User Validation

* Comment out `users.test.js` as we don't need it
* But for now keep it around for reference

* We need to test `users.js`

`users.js`

```
import SimpleSchema from 'simpl-schema';
import { Accounts } from 'meteor/accounts-base';

Accounts.validateNewUser((user) => {
  const email = user.emails[0].address;

  new SimpleSchema({
    email: {
      type: String,
      regEx: SimpleSchema.RegEx.Email
    }
  }).validate({
    email
  });

  return true;
});
```

* It is a `function` and we need to <u>simulate that function</u>
* It has one argument and checks if an **email is valid**
* If **email is valid** it returns `true`
* If **email isn't valid** it `returns an error`

## Restructuring Code
When you test, it is common to restructure your code

`users.js` (_restructured_)

```
import { Accounts } from 'meteor/accounts-base';
import SimpleSchema from 'simpl-schema';

const validateNewUser = (user) => {
  const email = user.emails[0].address;

  new SimpleSchema({
    email: {
      type: String,
      regEx: SimpleSchema.RegEx.Email,
    },
  }).validate({
    email,
  });

  return true;
};

Accounts.validateNewUser(validateNewUser);

export default validateNewUser;
```

* This functions exactly as before but this is easier to test

## Best Practice
* Have a root `describe()` block is always a good idea
* Don't have tests outside a `describe()` block

`users.test.js`

```
import expect from 'expect';

import { validateNewUser } from './users';

describe('users', function() {

  it('should allow valid email address', function () {
    // user.emails[0].address;
    const testUser = {
      emails: [
        {
          address: 'test@example.com'
        }
      ]
    };
    const res = validateNewUser(testUser);
    expect(res).toBe(true);
  });
});
```

* We see we pass on the Server but nothing on the Client
* If we check our console we'll see an error

![accounts error](https://i.imgur.com/E3MfNru.png)

* The reason is `Accounts.validateNewUser` only can run on **Server**
* So we should only run that function on the server

```
import SimpleSchema from 'simpl-schema';
import { Meteor } from 'meteor/meteor';
import { Accounts } from 'meteor/accounts-base';

export const validateNewUser = (user) => {
  const email = user.emails[0].address;

  new SimpleSchema({
    email: {
      type: String,
      regEx: SimpleSchema.RegEx.Email
    }
  }).validate({
    email
  });

  return true;
}

// add this check
if (Meteor.isServer) {
  Accounts.validateNewUser(validateNewUser);
}
```

### Our test passes on both! But...
Then we see our test passes on `the Client` and `the Server` but we only want to test on **the Server** because that is where it only can run

`users.test.js`

```
/* eslint-env mocha */
import expect from 'expect';
import { Meteor } from 'meteor/meteor';
import { validateNewUser } from './users';

if (Meteor.isServer) {
  describe('users', function() {

    it('should allow valid email address', function () {
      // user.emails[0].address;
      const testUser = {
        emails: [
          {
            address: 'test@example.com'
          }
        ]
      };
      const res = validateNewUser(testUser);
      expect(res).toBe(true);
    });
  });
}
```

## Test for invalid email
* Make sure we get an **error** when email is invalid
* We were checking for a value now we are checking for a function

```
// more code
  it('should reject invalid email', function() {
        expect(() => {
          throw new Error('Error message here');
        }).toNotThrow();
    });
// more code
```

## I demand you to throw an Error!
We want our function to throw an **error**

```
/* eslint-env mocha */
import expect from 'expect';
import { Meteor } from 'meteor/meteor';
import { validateNewUser } from './users';

if (Meteor.isServer) {
  describe('users', function() {

    it('should allow valid email address', function () {
      // user.emails[0].address;
      const testUser = {
        emails: [
          {
            address: 'test@example.com'
          }
        ]
      };
      const res = validateNewUser(testUser);
      expect(res).toBe(true);
    });

    it('should reject invalid email', function() {
        const testUser = {
          emails: [
            {
              address: 'meyou.com'
            }
          ]
        }

        expect(() => {
          validateNewUser(testUser);
        }).toThrow();
    });

  });
}
```

![we pass both tests](https://i.imgur.com/GnX0jT9.png)
