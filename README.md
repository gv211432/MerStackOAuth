# MerStackOAuth
Implementing OAuth in a MERN (MongoDB, Express.js, React, Node.js) stack involves several steps. Here's a simplified outline along with example code snippets to guide you through the process. 

### Step 1: Set Up Your OAuth Provider

1. **Register Your Application**: Go to the OAuth provider's website (e.g., Google, Facebook) and register your application. You'll receive a client ID and client secret.

### Step 2: Backend Setup (Node.js & Express)

2. **Install Dependencies**: Use npm to install necessary packages:
   ```bash
   npm install express express-session passport passport-google-oauth20 mongoose
   ```

3. **Configure Passport**:
   In your Express app, set up Passport with the OAuth strategy.
   ```javascript
   const passport = require('passport');
   const GoogleStrategy = require('passport-google-oauth20').Strategy;

   passport.use(new GoogleStrategy({
       clientID: YOUR_CLIENT_ID,
       clientSecret: YOUR_CLIENT_SECRET,
       callbackURL: "/auth/google/callback"
     },
     function(accessToken, refreshToken, profile, cb) {
       // Here, you can save the profile info to your DB
       return cb(null, profile);
     }
   ));
   ```

4. **Set Up Routes**:
   Create routes for authentication and callbacks.
   ```javascript
   app.get('/auth/google',
     passport.authenticate('google', { scope: ['profile', 'email'] }));

   app.get('/auth/google/callback', 
     passport.authenticate('google', { failureRedirect: '/login' }),
     function(req, res) {
       // Successful authentication, redirect home.
       res.redirect('/');
     });
   ```

### Step 3: Frontend Setup (React)

5. **Create Login Button**:
   In your React app, create a button to initiate authentication.
   ```jsx
   const handleLogin = () => {
     window.open("http://localhost:5000/auth/google", "_self");
   };

   return (
     <button onClick={handleLogin}>Login with Google</button>
   );
   ```

### Step 4: Handling User Sessions

6. **Configure Express Session**:
   Store user data in session.
   ```javascript
   const session = require('express-session');

   app.use(session({
     secret: 'secret',
     resave: false,
     saveUninitialized: true
   }));

   passport.serializeUser((user, done) => {
     done(null, user.id);
   });

   passport.deserializeUser((id, done) => {
     // Fetch user from DB using the id
     done(null, user);
   });
   ```

### Step 5: Connecting MongoDB

7. **Set Up Mongoose**:
   Connect to your MongoDB database and define user schema.
   ```javascript
   const mongoose = require('mongoose');
   mongoose.connect('mongodb://localhost:27017/yourDB', {useNewUrlParser: true});

   const userSchema = new mongoose.Schema({
     googleId: String,
     // other fields...
   });

   const User = mongoose.model('User', userSchema);
   ```

### Testing and Deployment

8. **Test Your Application**: Ensure that the OAuth flow is working as expected.

9. **Deployment**: Deploy your application, ensuring that you update the OAuth callback URL in your provider's settings.

Remember, this is a simplified example. Security, error handling, and user data management are critical aspects you'll need to consider for a production-ready application. Always store sensitive information like client secrets securely and never expose them in client-side code.
