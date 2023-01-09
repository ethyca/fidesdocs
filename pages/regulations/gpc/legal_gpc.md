import Callout from 'nextra-theme-docs/callout'

# For legal teams - How does my business comply with GPC?

At present, California’s Privacy Protection Agency, the [CPPA](https://cppa.ca.gov), is the first regulator to formally confirm that they expect businesses to comply with GPC as part of California privacy regulations. In demonstrating their willingness to enforce, in August 2022, [Sephora was fined $1.2M](https://iapp.org/news/a/the-sephora-case-do-not-sell-but-are-you-selling/) for not complying with data sales consent requirements including the Global Privacy Control.

More generally speaking, Ethyca’s recommended best practice is to interpret the GPC signal as contextually appropriate to the jurisdictions in which your business operates. In simple terms, if you are operating in Europe under GDPR, Brazil under LGPD and the growing list of US States such as California, Virginia, Colorado, Connecticut and Utah that have considerations around data sharing or targeted advertising, you should consider supporting the GPC signal as appropriate to that market. 

It is important to note that this decision is subjective and one a business must make, however in considering the consumers best interests, expectations and understanding of their rights to consent, here at Ethyca we believe that where a user opts-out using the GPC, they would reasonably expect to be opted out of:

* Sale of their data
* Sharing of their data for the purpose of advertising
* Targeted advertising

Conversely, you can see that many of these regulations also perform “automated decision making” or “profiling” which is something a business may reasonably expect to perform as part of the provision of its services; so of course you will need to provide a user the ability to consent, however depending on what you do within your automated decision making systems, this may not constitute something that a GPC opt-out signal should reasonably enforce.

Here’s a helpful chart of how to think about what the GPC is likely analogous to in each jurisdiction. As you can see, here we’ve mapped these to whether or not it would be reasonably expected by a consumer that the GPC should honor this data processing right.


## Table: How to map the GPC to any Global or State Privacy Regulation


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


**A note on Europe and Brazil:** An interesting point to note on the table above, while Europe’s consent requirements tend to be opt-in and don’t immediately map to the opt-out nature of GPC, thoughtful user experience considerations are vital.

If a user has set GPC to `true`, it would be reasonable to assume that they do not give opt-in consent to the advertising category of consent under European privacy regulations.

<Callout emoji="ⓘ">
If you're unsure how to setup GPC support you can ask the [Fides Slack Community](https://fid.es/join-slack), or get [Privacy Engineering Intelligence from Ethyca](https://ethyca.com/book-demo) now.
</Callout>