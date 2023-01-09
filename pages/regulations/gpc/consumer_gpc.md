import Callout from 'nextra-theme-docs/callout'

# For consumers, how does GPC work?

As a first step, privacy preferences can be set by the user in their browser if that browser supports the GPC standard.

The list of browsers that support GPC is continuously growing, as of this writing, this includes [Firefox Nightly](https://blog.mozilla.org/netpolicy/2021/10/28/implementing-global-privacy-control/gpc-firefox-nightly-step-1/), [Brave](https://brave.com/web-standards-at-brave/4-global-privacy-control/) and [DuckDuckgo](https://help.duckduckgo.com/duckduckgo-help-pages/privacy/gpc/). In addition to this, GPC has created a [Chrome Plugin](https://chrome.google.com/webstore/detail/gpc-enabler/ilknagnpcicckgohjailfooamibaolnj) to support GPC on Chrome. 

From their browser, the consumer sets their Global Privacy Control preference to `true`, meaning they opt out of data sales or sharing. From the consumer’s perspective, that’s all there is to it - when the user browses to any future websites, their browser shares a signal that confirms the user has opted out of data sales or sharing and your website must respect that signal and ensure the users personal data is appropriately protected and not processed in for this purpose.

<Callout emoji="ⓘ">
If you're unsure how to setup GPC support you can ask the [Fides Slack Community](https://fid.es/join-slack), or get [Privacy Engineering Intelligence from Ethyca](https://ethyca.com/book-demo) now.
</Callout>