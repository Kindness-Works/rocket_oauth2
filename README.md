# rocket_oauth2 for rocket 0.6.0-dev (rev f50b604)

![maintenance: passive](https://img.shields.io/badge/maintenance-passive-orange)
[![crates.io](https://img.shields.io/crates/v/rocket_oauth2)](https://crates.io/crates/rocket_oauth2)
[![docs.rs](https://docs.rs/rocket_oauth2/badge.svg)](https://docs.rs/rocket_oauth2/)

`rocket_oauth2` helps set up an OAuth 2.0 client in [Rocket](https://rocket.rs)
applications.

## Major Versions

* 0.4.x, compatible with `rocket` `0.4.x`, are based on the previous [`master` branch](https://github.com/jebrosen/rocket_oauth2/tree/master)
* 0.5.x, compatible with `rocket` `0.5.x`, are based on the newer [`main` branch](https://github.com/jebrosen/rocket_oauth2/tree/main)

## Quickstart Example

For more detailed examples and explanations, see the crate documentation and the
projects in the repository's `examples` directory.

### Code

```rust
use rocket::http::{Cookie, CookieJar, SameSite};
use rocket::Request;
use rocket::response::Redirect;
use rocket_oauth2::{OAuth2, TokenResponse};

struct GitHub;

#[get("/login/github")]
fn github_login(oauth2: OAuth2<GitHub>, cookies: &CookieJar<'_>) -> Redirect {
    oauth2.get_redirect(cookies, &["user:read"]).unwrap()
}

#[get("/auth/github")]
fn github_callback(token: TokenResponse<GitHub>, cookies: &CookieJar<'_>) -> Redirect
{
    cookies.add_private(
        Cookie::build(("token", token.access_token().to_string()))
            .same_site(SameSite::Lax)
            .build()
    );
    Redirect::to("/")
}

#[launch]
fn rocket() -> _ {
    rocket::build()
        .mount("/", routes![github_callback, github_login])
        .attach(OAuth2::<GitHub>::fairing("github"))
}
```

### Configuration (`Rocket.toml`)

```toml
[default.oauth.github]
provider = "GitHub"
client_id = "..."
client_secret = "..."
redirect_uri = "http://localhost:8000/auth/github"
```

## License

`rocket_oauth2` is licensed under either of the following, at your option:

 * Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
 * MIT License ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)
