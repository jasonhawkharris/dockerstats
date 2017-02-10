[![GoDoc](https://godoc.org/github.com/KyleBanks/dockerstats?status.svg)](https://godoc.org/github.com/KyleBanks/dockerstats) &nbsp;
[![Go Report Card](https://goreportcard.com/badge/github.com/KyleBanks/dockerstats)](https://goreportcard.com/report/github.com/KyleBanks/dockerstats)

# dockerstats
--
    import "github.com/KyleBanks/dockerstats"

Package dockerstats provides the ability to get currently running Docker
container statistics, including memory and CPU usage.

To get the statistics of running Docker containers, you can use the `CurStats()`
function:

    stats, err := dockerstats.CurStats()
    if err != nil {
    	panic(err)
    }

    for _, s := range stats {
    	fmt.Println(s.Container) // 9f2656020722
    	fmt.Println(s.Memory) // {Raw=221.7 MiB / 7.787 GiB, Percent=2.78%}
    	fmt.Println(s.CPU) // 99.79%
    }

Alternatively, you can use the `Monitor()` function to receive a constant stream
of Docker container stats:

    c := make(chan *StatsResult)
    dockerstats.Monitor(c)

    for {
    	res := <-c
    	if res.Error != nil {
    		panic(err)
    	}

    	for _, con := range res.Stats {
    		fmt.Println(con.Container) // 9f2656020722
    	}
    }

## Usage

#### func  CurStats

```go
func CurStats() ([]Stats, error)
```
CurStats returns the current stats of each running Docker container.

#### func  Monitor

```go
func Monitor(c chan *StatsResult)
```
Monitor repeatedly retrieves the current stats for each running Docker
container, and sends them through the channel provided.

#### type MemoryStats

```go
type MemoryStats struct {
	Raw     string
	Percent string
}
```

MemoryStats contains the statistics of a running Docker container related to
memory usage.

#### func (MemoryStats) String

```go
func (m MemoryStats) String() string
```
String returns a human-readable string containing the details of a MemoryStats
value.

#### type Stats

```go
type Stats struct {
	Container string
	Memory    MemoryStats
	CPU       string
}
```

Stats contains the statistics of a currently running Docker container.

#### func (Stats) String

```go
func (s Stats) String() string
```
String returns a human-readable string containing the details of a Stats value.

#### type StatsResult

```go
type StatsResult struct {
	Stats []Stats
	Error error
}
```

StatsResult is the value recieved when using Monitor to listen for Docker
statistics.