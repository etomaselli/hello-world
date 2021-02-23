# hello-world

Hi there,
pigna pizzicotto manicotto tigre.

```
services.coordinator.web.auth: {
  type: "oauth", # Possible values are internal, oauth
  oauth: {
      authorizationUrl: "https://aac.kube-test.smartcommunitylab.it/eauth/authorize"
      tokenUrl: "https://aac.kube-test.smartcommunitylab.it/oauth/token"
      userInfoUrl: "https://aac.kube-test.smartcommunitylab.it/userinfo"
      callbackUrl: "http://localhost:9047"
      clientId: "cf066d3c-96a3-4da3-83da-c550d8f4b24e"
      clientSecret: "92bd2d2a-b7dd-4008-916c-e0b8914ca3ed"
      tenantField: "dremio/username"
      scope: "openid profile email user.roles.me"
  }
}
```
