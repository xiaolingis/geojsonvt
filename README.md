# geojsonvt

### Movitivation 

I've implemented geojson-vt in the past, but my implementation went from slicing tiles based a map view and returning them to creating all a tileset from a data source. While creating a tileset is something useful that I want to do, a lot of the advantages for why I wanted to use geojsonvt needed to operate as stand alone functions to profile and debug better. This also simplified the libaries structure as well. 

### What it does

Given a set of geojson features returns a tile structure that can either be drilled into children tiles or marshaled into bytes. 

### Usage 

```go 
package main

import (
	"fmt"
	"github.com/murphy214/geojsonvt"
	m "github.com/murphy214/mercantile"
	"github.com/paulmach/go.geojson"
	"io/ioutil"
)

func main() {
	// reading in features
	bytevals, err := ioutil.ReadFile("county.geojson")
	if err != nil {
		fmt.Println(err)
	}
	fc, err := geojson.UnmarshalFeatureCollection(bytevals)
	if err != nil {
		fmt.Println(err)
	}
	features := fc.Features

	// configuration structure
	options := geojsonvt.NewConfig()

	// getting tile x = 4, y = 6, z = 4
	tile := geojsonvt.TileFromGeoJSON(features, m.TileID{4, 6, 4}, options)

	// marshaling to protobuf
	bytevals = tile.Marshal() // the protocol buffer byte values
	fmt.Println(len(bytevals))

	// getting the 4 tiles below it if available
	tilemap := tile.SplitTileChildren() // map[m.TileID][]*geojson.Feature
	fmt.Println(len(tilemap))
}

```
