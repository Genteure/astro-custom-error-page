# demo for returning content of another page

Goal:

- When user visits `/blog/notfound`, they should see the same page as `/why-are-you-here` or any other page that doesn't exist.
- When user visits `/blog/fail`, they should see the same page as `/502`.


```mermaid
sequenceDiagram
    actor u as User
    participant e as Express with middleware<br>or Caddy or Nginx
    participant a as Astro
    participant s as API Server
    alt page doesn't exist in Astro
        u->>e: /notfound
        e->>a: /notfound
        a-->>e: 404 with html
        e-->>u: 404 with html
    else API returns 200
        u->>e: /blog/content
        e->>a: /blog/content
        a->>s: /api/blog?id=content
        s-->>a: HTTP 200
        a-->>e: 200 with html
        e-->>u: 200 with html
    else API returns 404
        u->>e: /blog/notfound
        e->>a: /blog/notfound
        a->>s: /api/blog?id=notfound
        s-->>a: HTTP 404
        a-->>e: 404 with empty body
        e->>a: /404
        a-->>e: 200 with html
        e-->>u: 404 with html
    else API offline
        u->>e: /blog/content
        e->>a: /blog/content
        a->>s: /api/blog?id=content
        s--xa: API Server offline
        a-->>e: 502 with empty body
        e->>a: /502
        a-->>e: 200 with html
        e-->>u: 502 with html
    end
```