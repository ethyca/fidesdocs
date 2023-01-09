import Callout from 'nextra-theme-docs/callout'

# GPC Step 03:
# Enforce user GPC preferences across all systems
In some cases such as advertising technology and platform providers who support standards such as IAB CCPA compliance of the TCF String, these vendors will already respect an opt-out signal if shared with them.

However, many of your vendors that process data on your behalf will not support this by default and you will need to ensure that you enforce the users opt-out preferences on those systems.

You should think about enforcing this in a few ways:

<ol>
    <li>Use custom (custom JS) conditions and triggers in  your Google Tag Manager, Adobe Tag Manager or Segment CDP</li>
    <li>Use a manual process for offline systems to add users to suppression lists or opt-out lists to ensure they are not processed</li>
    <li>Use [Fides](https://ethyca.com/book-demo) to automatically configure opt-out enforcement for all systems across your technology stack.</li>
</ol>


    <Callout emoji="ⓘ">
    If you're unsure how to setup GPC support you can ask the [Fides Slack Community](https://fid.es/join-slack), or get [Privacy Engineering Intelligence from Ethyca](https://ethyca.com/book-demo) now.
    </Callout>
