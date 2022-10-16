# dfevents

A multi-handler system for Dragonfly (part of [GracticeEssential](https://github.com/Blackjack200/GracticeEssential)).

# How it works

This section will show the basics of how dfevents works.

Firstly you will need to make a new *MultipleHandler. This will store all registered handlers, and will allow you to
register/unregister handlers.

A handler is just a struct that implements some methods fom player.Handler. Note that your handler does not actually need to implement player.Handler. In fact, it is recommended to not implement events you dont use for performance reasons.

```go
package main

import (
	"github.com/blackjack200/dfevents/mhandler"
	"github.com/df-mc/dragonfly/server/player"
)

type myChatHandler struct {
	unreg func()
}

func (m myChatHandler) HandleChat(ctx *event.Context, msg *string) {
	*msg = "You can break block now"
	m.unreg()
}

type myBlockBreakHandler struct{}

func (myBlockBreakHandler) HandleBlockBreak(ctx *event.Context, _ cube.Pos, _ *[]item.Stack) {
	ctx.Cancel()
}

type myQuitHandler struct{}

func (myQuitHandler) HandleQuit() {
	logrus.Info("Quited")
}

func init() {
	var p *player.Player
	mhdr := mhandler.New()
	p.Handle(mhdr)
	unreg := mhdr.Register(myBlockBreakHandler{})
	mhdr.Register(myChatHandler{unreg: unreg})
	mhdr.Register(myQuitHandler{})
}
```
