# Singed

Singed makes it easy to get a flamegraph anywhere in your code base:

## Usage

To profile your code, and launch [speedscope](https://github.com/jlfwong/speedscope) for viewing it:

```ruby
flamegraph {
  # your code here
}
```

Flamegraphs are saved for later review to `Singed.output_directory`, which is `tmp/speedscope` on Rails. You can adjust this like:

```ruby
Singed.output_directory = "tmp/slowness-exploration"
```

If you are calling it in a loop, or with different variations, you can include a label on the filename:

```ruby
flamegraph(label: "rspec") {
  # your code here
}
```

You can also skip opening speedscope automatically:

```ruby
flamegraph(open: false) {
  # your code here
}
```

If you are using RSpec, you can use the `flamegraph` metadata to capture it for you.

```ruby
# make sure this is required at somepoint, like in a spec/support file!
require 'singed/rspec' 

RSpec.describe YourClass do
  it "is slow :(", flamegraph: true do
    # your code here
  end
end
```

If you want to capture a flamegraph of a controller action, you can call it like:

```ruby
class EmployeesController < ApplicationController
  flamegraph :show

  def show
    # your code here
  end
end
```

This won't catch the entire request though, just once it's been routed to controller and a response has been served.

To capture the whole request, there is a middleware which checks for the  `X-Singed` header to be 'true'. With curl, you can do this like:

```shell
curl -H 'X-Signed: true' https://localhost:3000
```

PROTIP: use Chrome Developer Tools to record network activity, and copy requests as a curl command. Add `-H 'X-Singed: true'` to it, and you get flamegraphs!
## Limitations

When using the auto-opening feature, it's assumed that you are have a browser available on the same host you are profiling code.

The `open` is expected to be available.

## Alternatives

- running [rbspy](https://rbspy.github.io/)
- using [stackprof](https://github.com/tmm1/stackprof) (a dependency of singed) directly