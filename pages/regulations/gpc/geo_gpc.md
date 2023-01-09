import Callout from 'nextra-theme-docs/callout'

# GPC Step 02:
# Define which geography to apply GPC
While both California’s Consumer Privacy Act (CCPA) and Calififornia’s Privacy Rights Act (CPRA) should abide by the GPC by default, it’s important to consider whether you will afford these rights to other jurisdictions, whether that is other US states or other global jurisdications.

The following table provides an example of how to think about where to provide support for GPC based on users preferences of consent.

<table>
<thead>
  <tr>
   <td>**Jurisdiction**</td>
   <td>**Regulation**</td>
   <td>**Data Process**</td>
   <td>**Mechanism**</td>
   <td>**GPC Suitability**</td>
  </tr>
  </thead>
  <tbody>
  <tr>
   <td>California, USA</td>
   <td>CCPA</td>
   <td>Data Sales</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>California, USA</td>
   <td>CPRA</td>
   <td>Data Sharing</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>California, USA</td>
   <td>CPRA</td>
   <td>Automated Decision Making</td>
   <td>Opt-out</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Virginia, USA</td>
   <td>VCDPA</td>
   <td>Data Sales</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Virginia, USA</td>
   <td>VCDPA</td>
   <td>Targeted Advertising</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Virginia, USA</td>
   <td>VCDPA</td>
   <td>Profiling</td>
   <td>Opt-out</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Connecticut, USA</td>
   <td>CTDPA</td>
   <td>Data Sales</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Connecticut, USA</td>
   <td>CTDPA</td>
   <td>Targeted Advertising</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Connecticut, USA</td>
   <td>CTDPA</td>
   <td>Automated Decision Making</td>
   <td>Opt-out</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Colorado, USA</td>
   <td>CPA</td>
   <td>Data Sales or Sharing</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Colorado, USA</td>
   <td>CPA</td>
   <td>Targeted Advertising</td>
   <td>Opt-out</td>
   <td>**YES**</td>
  </tr>
  <tr>
   <td>Colorado, USA</td>
   <td>CPA</td>
   <td>Automated Decision Making</td>
   <td>Opt-out</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Europe</td>
   <td>GDPR / ePrivacy</td>
   <td>Essential</td>
   <td>Mandatory</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Europe</td>
   <td>GDPR / ePrivacy</td>
   <td>Functional</td>
   <td>Opt-in</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Europe</td>
   <td>GDPR / ePrivacy</td>
   <td>Analytics</td>
   <td>Opt-in</td>
   <td>**NO**</td>
  </tr>
  <tr>
   <td>Europe</td>
   <td></td>
   <td>Advertising</td>
   <td>Opt-in</td>
   <td>**YES**</td>
  </tr>
  </tbody>
</table>

You can do this by implementing your own geolocation via IP detection solution to identify the user’s location and appropriately respect their rights, or you can use [Fides](https://ethyca.com/book-demo) to automate this task and adjust jurisdictions as regulations evolve with ease.

    <Callout emoji="ⓘ">
    If you're unsure how to setup GPC support you can ask the [Fides Slack Community](https://fid.es/join-slack), or get [Privacy Engineering Intelligence from Ethyca](https://ethyca.com/book-demo) now.
    </Callout>
