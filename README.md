# ammo

HTTP Range processing utilities.

[![Build Status](https://travis-ci.org/hapijs/ammo.svg?branch=v2-commercial)](https://travis-ci.org/hapijs/ammo)

## License

This version of the package requires a commercial license. You may not use, copy, or distribute it without first acquiring a commercial license from Sideway Inc. Using this software without a license is a violation of US and international law. To obtain a license, please contact [sales@sideway.com](mailto:sales@sideway.com). The open source version of this package can be found [here](https://github.com/hapijs/ammo).

## Usage

```js
// basic usage
const range = Ammo.header('bytes=1-5', 10);
// range --> [{ from: 1, to: 5 }]

// multiple ranges
const range = Ammo.header('bytes=1-5,7-10', 10);
// range --> [{ from: 1, to: 5 }, { from: 7, to: 9 }]

// streams (get range within a `source`)
const range = Ammo.header('bytes=1000-4000', 5000);
const stream = new Ammo.Stream(range[0]);
Wreck.read(source.pipe(stream), {}, (err, buffer) => {
    // buffer is the portion of source within range

});
```

## API

### `header(header, length)`

Parses the range from a HTTP header.

* `header` - A string in the form of `bytes=from-to`, where `from` and `to` are
integers specifying the range. Both are optional. Multiple ranges can be passed
as a comma delimited list.
* `length` - A positive integer specifying the maximum length the range can
cover. If a `to` value passed in the `header` string is greater than `length`,
the `to` value is set as `length - 1`.

Returns an array of objects with the properties `from` and `to`, which specify
the beginning and ending of the range. Overlapping ranges are combined into one
object. Returns `null` for invalid input.

### `new Ammo.Stream(range)`

Creates a [`Transform Stream`](https://nodejs.org/api/stream.html) that extracts
the portion of a piped in stream within `range`.
* `range` - an object with the properties `from` and `to` that specify the range
of the piped in stream to read. Objects returned by `Ammo.header` can be passed
into `range`.
