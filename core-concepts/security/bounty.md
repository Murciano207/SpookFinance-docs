# Bug Bounty

## Overview

Yogi is working on a complete smart contract audits with CertiK. We also will run a continuous bug bounty program for the bronze release of Yogi core.

## Scope

The bug bounty covers any of the core smart contracts deployed on Mainnet. The code can be found at: [https://github.com/yogi-fi/yogi-core](https://github.com/yogi-fi/yogi-core)

Mainnet BFactory can be found at: [https://bscscan.com/address/0x2Fab27B3B379b550AED296B7Fb48D7EfEAabda57](https://bscscan.com/address/0x2Fab27B3B379b550AED296B7Fb48D7EfEAabda57)

_Additional second layer contracts, such as the exchange proxy or individual smart pool contracts, may be added at a further date._

## Rewards

The bounty program will pay out rewards according to the severity of a vulnerability. The final reward amount is at the sole discretion of Yogi Studio. See eligibility section below for more details.

<table>
  <thead>
    <tr>
      <th style="text-align:left">Reward</th>
      <th style="text-align:left">Severity</th>
      <th style="text-align:left">Examples</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>$20,000 - $50,000</b>
      </td>
      <td style="text-align:left">Critical</td>
      <td style="text-align:left">
        <ul>
          <li>Stealing assets from a pool</li>
          <li>Permanently freezing pool assets</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>$10,000 - $20,000</b>
      </td>
      <td style="text-align:left">High</td>
      <td style="text-align:left">
        <ul>
          <li>Severe rounding errors where an attacker can steal significant funds in
            excess of any gas costs or swap fees</li>
          <li>Manipulating a finalized pool&apos;s assets / weights / fees</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>$2,000 - $5,000</b>
      </td>
      <td style="text-align:left">Medium</td>
      <td style="text-align:left">
        <ul>
          <li>Minor rounding errors that allow an attacker to slowly manipulate funds
            to their advantage</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>$0 - $2,000</b>
      </td>
      <td style="text-align:left">Low</td>
      <td style="text-align:left">
        <ul>
          <li>Informational and code quality based disclosures</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>

## Reporting / Disclosures

Please report any findings to [yogi.fi@protonmail.com](mailto:yogi.fi@protonmail.com), with full details about any vulnerability and steps / code to reproduce. Allow us time to review and remediate any findings before public disclosure.

## Ineligible Findings

* Duplicate vulnerabilities. Only the first reporter will be rewarded.
* Findings already known as part of a formal audit.
* Findings related to non-standard BEP20 tokens might be ineligible as many vulnerabilities might be inserted in non-standard BEP20 tokens on purpose for applying for this bug bounty. 

