This code generates call stack message excluding the framework calls.


```
package debugger

import (
	"fmt"
	"runtime"
	"strings"
)

func GetStackTrace() string {
	// Ask runtime.Callers for up to 10 PCs, including runtime.Callers itself.
	pc := make([]uintptr, 10)
	n := runtime.Callers(0, pc)

	pc = pc[2:n] // pass only valid pcs to runtime.CallersFrames
	frames := runtime.CallersFrames(pc)

	stackTrace := ""

	// Loop to get frames.
	// A fixed number of PCs can expand to an indefinite number of Frames.
	for {
		frame, more := frames.Next()
		if strings.Contains(frame.Function, "runtime.Callers") ||
			strings.Contains(frame.Function, "runtime.goexit") {
			continue
		}
		if frame.PC != 0 {
			stackTrace += fmt.Sprintf("- %s:%d\n\t> %s\n", frame.File, frame.Line, frame.Function)
		}

		// Check whether there are more frames to process after this one.
		if !more {
			break
		}
	}

	return stackTrace
}

```
