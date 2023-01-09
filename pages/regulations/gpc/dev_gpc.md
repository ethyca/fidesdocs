import Callout from 'nextra-theme-docs/callout'

# For developers, how does GPC work?
As a first step, preferences are set in a user's browser. (Note: for technical purposes browsers are referred to as user agents.)

## How does the user set their GPC preference in their browser?
    When the user sets their Global Privacy Control preferences to `true` on their browser (user agent), the browser will ensure that any outbound HTTP request carries with it an additional header field called **_[Sec-GPC](https://globalprivacycontrol.github.io/gpc-spec/#dfn-sec-gpc)._**


## How does the browser transmit the GPC signal?
    The Sec-GPC header field should only be created and set by the browser when the users Global Privacy Control preference is set to `true`. In this case, where the user’s preference is true, the Sec-GPC header field will be set to `1`, analogous to `true`.

    As such, a valid HTTP request passing the Global Privacy Control will look something like:


```json
    GET /something/here HTTP/2
    Host: example.com
    Sec-GPC: 1
```



## How should my website detect and manage the GPC?
    Detecting the Global Privacy Control from Javascript properties is quite straightforward. The globalPrivacyControl property enables any client-side javascript to confirm the user's current settings for the Sec-GPC field value.

    The standard WebIDL that GPC provides for this is:


```
    interface mixin GlobalPrivacyControl {
      readonly attribute boolean globalPrivacyControl;
    };
    Navigator includes GlobalPrivacyControl;
    WorkerNavigator includes GlobalPrivacyControl;
```

    The value of this is `false` if no `Sec-GPC` header field is sent, otherwise the value will be `true`. 

    From this, checking the users current state is a case of checking GPC via a script such as the example below:

```json
    if (!navigator.globalPrivacyControl) {
      // wonderful, we can share or process the users data
    }
```

## How can I show my Website supports the GPC?
    While it’s not mandatory, the best practises provided by the Global Privacy Protocol recommend that your site provide a .well-known URL to represent that you abide by the GPC. 

    In practise this means that to validate that your abide by the GPC, you would provide a JSON object located at the location:


```json
    /.well-known/gpc.json
```

    The contents of this JSON simply states whether you respect the GPC as `true` or `false` and the timestamp for when this resource was last updated.

    An example of this provided by the GPC will look like this:


```json
    Content-Type: application/json

    {
      "gpc": true,
      "lastUpdate": "1997-03-10"
    }
```

    <Callout emoji="ⓘ">
    If you're unsure how to setup GPC support you can ask the [Fides Slack Community](https://fid.es/join-slack), or get [Privacy Engineering Intelligence from Ethyca](https://ethyca.com/book-demo) now.
    </Callout>
