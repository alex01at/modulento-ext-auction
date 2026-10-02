# Auctions

An extension for [Modulento](https://github.com/alex01at/modulento) that turns
the catalogue into an auction house.

It adds the offer type `auction.lot`: a lot with a starting price and a
minimum step between two bids that runs for 1 to 14 days from the moment its
offer is published. Logged-in visitors place binding bids; of two bids at the
same moment exactly one wins, and a bid in the last two minutes extends the
end so that the others can answer. When the time is up, the highest bid
becomes an order, and the order flow covers handover, confirmation of receipt
(automatic after 14 days) and cancellation. A lot without a bid is paused and
can be published again for a new run. Outbid bidders and providers of unsold
lots are told by e-mail, and the offer page counts down the time left.

## Requirements

Modulento 0.11.0 or newer, which provides interface version 1 in the form
this extension uses (`OrderFlow::checkout()` exists since that release).

**The cron has to run.** Auctions are closed by the scheduled task
`auction.close`, which runs every minute; without a trigger for Modulento's
scheduled tasks no lot ever ends and no order is created.
**Administration → Tasks** shows how to set it up.

## Installing

In Modulento, open **Administration → Packages**, enter
`alex01at/modulento-ext-auction` and install. Then enable "Auctions" under
**Administration → Extensions**; this creates the tables. New versions
appear on the Packages page.

By hand: unpack a release into `extensions/auction/` of the installation and
enable the extension.

## Data

The extension keeps its data in tables of its own, all starting with
`x_auction_`: `x_auction_lot` and `x_auction_bid`. They reference the core's
offers and go with them. The bids of an account are part of its data export
and are removed with the account. Removing the package leaves the tables in
place.

## Changing the look

The templates in `templates/` are rendered as `@auction/<file>.twig`. Do not
edit them here - an update would replace the files. A theme overrides a
template by bringing a file of the same name in
`themes/<theme>/extensions/auction/`.

## Development

The tests are part of the Modulento repository and expect the extension at
`extensions/auction` there - for example as a symlink to this clone:

```
ln -s /path/to/modulento-ext-auction /path/to/modulento/extensions/auction
cd /path/to/modulento && php tests/run.php
```

This repository itself only checks the syntax of its PHP files on every push.

## Releasing

Set `version` in `extension.json`, commit, then tag and push:

```
git tag v0.2.1 && git push origin main v0.2.1
```

The workflow checks that tag and `extension.json` agree, builds
`modulento-ext-auction-<version>.zip` with its SHA-256 and publishes both as
a GitHub Release.

## Licence

GPL-3.0-or-later, see `LICENSE`.
