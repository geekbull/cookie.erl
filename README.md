## cookie.erl

Simple library that allows cookies to be deserialized from header proplists and serialized into
a form suitable for inclusion in subsequent requests. Cookie attributes can be queried, including
validity through checking of the expire time.

## Rebar Installation

Add the following to your rebar.config:

```erlang
{ cookie, ".*",	{ git, "git://github.com/wrren/cookie.erl.git", { branch, "master" } } }
```

## Examples 

```erlang

Headers = [
		{ <<"Content-Type">>, <<"text/javascript">> },
		{ <<"Content-Length">>, <<"64">> },
		{ <<"Set-Cookie">>, <<"cookie1=hello; expires=Wed, 17-May-2199 12:55:30 GMT; path=/; domain=.example.com; HttpOnly">> },
		{ <<"Set-Cookie">>, <<"cookie2=world; path=/; secure; SameSite=Strict">> }
],
{ ok, [ Cookie1, Cookie2 ] } = cookie:deserialize( Headers ),
Name 	= cookie:name( Cookie1 ), 		%% cookie1
Value 	= cookie:value( Cookie1 )		%% hello
Expires = cookie:expires( Cookie1 ),    %% 123456789
MaxAge	= cookie:max_age( Cookie1 ),	%% undefined  
Secure	= cookie:secure( Cookie1 ),		%% false
Path 	= cookie:path( Cookie1 ),		%% /
Domain	= cookie:domain( Cookie1 ),		%% .example.com
Http 	= cookie:http_only( Cookie1 ),	%% true

Serialized 	= cookie:serialize( [ Cookie1, Cookie2 ] ).	%% cookie1=hello; cookie2=world 

```

### Deserialize

Deserializes a list of cookies from a given proplist containing header key-value pairs. Set-Cookie headers are matched and
their values are parsed in order to generate cookie structures.

```erlang
cookie:deserialize( [ { HeaderKey :: binary(), HeaderValue :: binary() } ] ) -> { ok, [ Cookie ] } | { error, Reason }.
```

### Serialize

Serializes a list of cookies into a form suitable for inclusion in request headers

```erlang
cookie:serialize( [ Cookie ] ) -> Serialized :: binary()
```

### Merge

Given a set of cookies received from a new request, merge their values into an old set of cookies, overwriting any
cookies in the old list with matching names.

```erlang
cookie:merge( OldCookies :: [ Cookie ], NewCookies :: [ Cookie ] ) -> [ Cookie ].
```