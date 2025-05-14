# haskelweb

# minimalistická web api

myšlenka je vytvořit na začátku monad reprezentující web app a postupně ho modifikovat middlewarem něco jako minim api v ASP.NET

```haskell
type Handler = WebApp ()
type Middleware = Handler -> Handler

infixl 1 |->

(|->) :: Handler -> Middleware -> Handler
handler |-> middleware = middleware handler
```

---

## Web Context & Monad

```haskell
data WebContext = WebContext
  { request     :: Request
  , response :: Response
  , config      :: AppConfig
  , routeParams :: Map Text Text
  }

newtype WebApp a = ... some monad

type WebAppBuilder = ...
```

---

## Application & Routing

```haskell
data Route = Route
  { method    :: Method
  , path      :: Text
  , middlewareStack :: [Middleware]
  , handler   :: Handler
  }

data AppState = AppState
  { routes      :: [Route]
  , middlewares :: [Middleware]
  }

data ServerConfig = ServerConfig
  { port :: Int
  }
```

---

## DSL

```haskell
-- run web server with the given config
runWebServer :: ServerConfig -> WebAppBuilder -> IO ()

-- add middleware
use :: Middleware -> WebAppBuilder

-- define routes for specific HTTP methods
get    :: Text -> Handler -> WebAppBuilder
post   :: Text -> Handler -> WebAppBuilder
put    :: Text -> Handler -> WebAppBuilder
delete :: Text -> Handler -> WebAppBuilder

-- group routes
group :: Text -> WebAppBuilder -> WebAppBuilder
```

---

## Middleware Examples

```haskell
logger      :: Middleware
staticFiles :: FilePath -> Middleware
...
```

---

## Request Utilities

```haskell
param      :: Request -> Text -> Text
getHeader  :: Text -> WebApp (Maybe Text)
...
```

---

## Response Utilities

```haskell
textResponse  :: Text -> Response
htmlResponse  :: Text -> Response
...
```
