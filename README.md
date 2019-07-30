# Preview & Live Reload Server for Slick

This is very simple package for [Slick](https://github.com/ChrisPenner/slick) project with serving functionality. System spawns http and websocket server that allows to locally preview site on your computer and see live updates if javascript script integrated in `Slick` builder.

Package is within `Slick` namespace.

## Example

1. Define approprate flags to your Slick builder, for this purpose you'll need to send flags as argument

```haskell
data Flags =
    Preview        -- ^ key to run server
  | SomeOtherFlag  -- ^ watch for changes
  deriving (Eq)

flags :: forall a. [OptDescr (Either a Flags)]
flags =
  [ Option "" ["preview"        ] (NoArg $ Right Preview      ) "running as preview"
  , Option "" ["some-other-flag"] (NoArg $ Right SomeOtherFlag) "doing something else"
  ]
```

2. Use `preview` functionality for your builder like this

``` haskell
 -- call Shake builder with explicit flags arguments
 shakeArgsPruneWith shOpts pruner flags $
   \flags targets -> do
     let isPreviewMode = Preview `elem` flags

	 when isPreviewMode $ do
      action $ runAfter $ liftIO $ void . forkIO $ do
      putStrLn $ "Running with Preview"
      serverStart "public" "127.0.0.1" 3030 serverHandler

     -- other build rules for Shake to build site

```

3. Additionally, integrate `js/live-reload.js` script in your templates for `live reloading` functionality. It consume updates from webscoket server and on `update` message automatically reloads page.
