---
layout: post
comments: true
title: Building a Mario demo with React
---

This was a fun weekend project, where I ported a Mario demo written in [PureScript](https://github.com/michaelficarra/purescript-demo-mario){:target="_blank"} to [React](https://github.com/adrianwong/react-mario){:target="_blank"}.

Here are some learnings from this exercise:
* This would have been easier to write in vanilla JS. This demo was so simple that there was never much risk of creating spaghetti state, which is a problem that React purportedly solves.
* Identify where state should live:
  * Most of the state is owned by the main `Game` component. The `Player` is in fact a 'dumb' component, that is rendered based on positional values that are passed in as props.
  * The `Hud` is another example of a 'dumb' component, that renders the player's score and remaining lives from values that are passed in as props.
  * The `Background` component is responsible for changing the background image every 10 seconds. It is unnecessary to have the parent `Game` component maintain this state, so it maintains its own.
* It is necessary to call an `update` function on every frame using `requestAnimationFrame` to ensure smooth animation.
* The `zoom` CSS property is non-standard, and is not supported by the Firefox browser.
