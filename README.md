# mcfly-primo-yamr-delorean

## TODO

- prev/next wrap
  - visibly clickable
  - better appearance of title, time
  - call out current more distinctly
  - "no revision" message full height
- metrics, events, neightbors blobs
- arbitrary diff selection?


- ! event entity Link fields dont have type set (cant follow entity links)
- special fields callout (neighbors, events, metrics)
  - or at least make them collapsible, scrollable, something
  - or move them down below details (Separate handling)

- dont swap type/id till request finished
- dont diff on metric/event?
- toggle prev/next diffs?
- select a/b to diff in timeline?

- speedometer or flux capacitor animation
- look at `exists` and do stuff
- blacklist fields from generating diffs
- revised fields in timeline *
- nicely diff array values *
- store/load selected snapshot state in URL *


## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
