# Invisible Recaptcha Server-Side Validator
Simple Express Server for invisible reCaptcha server-side validation. Works nicely for static websites, in my case — a Jekyll website hosted on GitHub Pages. 


## Instructions
1. Register your site/ get your reCaptcha keys [here](https://www.google.com/recaptcha)
2. Create a new [Heroku App](https://dashboard.heroku.com/apps)
3. In your new Heroku App, go to settings and create a new Config Var called `RECAPTCHA_SECRET` and enter your secret key in the value field
4. Clone this repo
5. Deploy your app via the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli) or hook up your GitHub repository via the Deploy tab in your Heroku App
6. After you deploy your app, your app url should display 'reCaptcha verification'


## Client-Side Instructions
1. Paste this snippet in the `head` of your html template: `<script src='https://www.google.com/recaptcha/api.js'></script>`
2. Choose your [method for integration](https://developers.google.com/recaptcha/docs/invisible#explicit_render). I ultimately went with the 'Programmatically invoke the challenge' method to have control over when the reCaptcha validation happens (I needed it to happen after the form field validation).
3. Whichever method you choose, set your `callback-funtion` to `onSubmit`.
4. Here is what your onSubmit funtion should look like:

<pre><code>function onSubmit(token) {
  submitForm();
}
</code></pre>

5. Now we need to grab our token and send it to our Heroku app. Our app will read the token, send it to Google along with our secret key, get a response and send it back to us with a pass or fail.
<pre><code>function submitForm(){
  console.log('Initialize Server-Side Validation');
  
  // Note that we grab the value of '#g-recaptcha-response' and not just '#g-recaptcha'
  var captcha = document.querySelector('#g-recaptcha-response').value;

  fetch('YOUR HEROKU APP URL HERE', {
    method:'POST',
    headers: {
      'Accept': 'application/json, text/plain, */*',
      'Content-type':'application/json'
    },
    body:JSON.stringify({captcha: captcha})
  })
  .then((res) => res.json())
  .then((data) => {
    console.log(data.success);
    if( data.success === true ){
      document.getElementById("my-form").submit();
    }
  });
}
</code></pre>
