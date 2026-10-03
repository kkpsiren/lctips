# LCTips

This repository is [kkpsiren/lctips](https://github.com/kkpsiren/lctips).
It is a fork of [JaviEzpeleta/lctips](https://github.com/JaviEzpeleta/lctips).

LCTips is a Next.js app for Lens Chain transfers.
You search for a Lens handle.
The app shows sent and received GHO, BONSAI, and POINTLESS amounts.
The app metadata uses [https://lctips.xyz](https://lctips.xyz).

## Fork changes

The two latest commits belong to this fork.

`Classify transfer sources` adds `classifyTransferSource` in `lib/transfer-source.ts`.
The function reads transaction logs.
The function returns a `TransferSource`.
The detail API stores that source on the transfer.
A source kind can be `profile_tip`, `post_tip`, `collect`, `fundraising`, `prediction`, or `competition`.
A source kind can also be `swap`, `bridge`, `known_system_transfer`, `unknown_post_action`, or `unknown_account_action`.
Other transfers stay `unknown`.
`DetailTransferRow` shows `source.label`.
`contextualizeTransferSource` sets a bridge label to `Bridge out` or `Bridge in`.

`Speed up transfer fetching` caches explorer pages.
The same change loads those pages together.
`getTransfers` keeps each page for 60 seconds.
The cache key is the address and the page number.
The cache holds at most 2000 keys.
A failed fetch removes that cache entry.
`useProgressiveTransfers` requests two pages at the same time.
One burst requests at most eight pages.
Loading stops at 5000 transfers.

`getTransfers` and `getOutcomeTransfers` start at `2026-01-01` UTC.
Each explorer request asks for 100 transfers.
Edit that start date in `lib/lens-explorer.ts` when you need older transfers.

## Behavior

Search waits 300 ms.
The app then queries Lens profiles.
The profile API groups amounts by the other address.
That API omits a grouped total below 0.01.
The detail page loads `/api/detail-transfers-page`.
A counterparty row links to another profile.
The rewards page lists recipients from the 2025 Lens Chain rewards program.
`/thank-you` plays a video from exported GHO ranking JSON.
The export keeps the top 20 GHO counterparties.
The UI shows `WGHO` and `ETH` amounts as GHO.
The landing page says most Lens clients no longer support BONSAI tipping.
The app still shows BONSAI transfers.
POINTLESS rows use the symbol `pointless`.
`sticker-library/` holds the Lensie sticker packs for the thank-you video.
`sticker-library/INDEX.md` lists 10 packs and 268 stickers.
The default export requests Gemini notes for the top 10 ranks.
That request uses `GEMINI_API_KEY`.
The video still runs when that key is absent.

## Stack

The app uses Next.js 15, React 19, and TypeScript.
`npm run dev` starts Next.js with Turbopack.
Styles use Tailwind CSS v4 and `tw-animate-css`.
The UI uses Radix UI, Framer Motion, Lucide, and `@number-flow/react`.
Lens profile calls use `@lens-protocol/client` and `@lens-protocol/react`.
Wallets use wagmi and ConnectKit.
Chain reads use ethers and `https://rpc.lens.xyz`.
Transfer history uses `https://explorer-api.lens.xyz`.

## Setup

Install Node.js and npm.
Clone this fork.

```bash
git clone https://github.com/kkpsiren/lctips.git
cd lctips
npm install
cp .env.example .env.local
```

Set `NEXT_PUBLIC_WALLET_CONNECT_PROJECT_ID` in `.env.local`.
Create that id at [cloud.walletconnect.com](https://cloud.walletconnect.com).
Set `GEMINI_API_KEY` only when you want generated thank-you notes.

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

```bash
npm run lint
npm run build
npm start
```

`npm run lint` runs `next lint`.
`npm run build` creates the production build.
`npm start` serves that build.

## Layout

- `app/` holds the App Router pages and API routes.
- `app/(tips)/u/[handle]` is the profile detail page.
- `app/(tips)/u_legacy/[handle]` is the older profile page.
- `app/(tips)/rewards` is the rewards page.
- `app/thank-you` is the thank-you studio.
- `app/api/profile` groups profile tip totals.
- `app/api/detail-transfers-page` returns one classified transfer page.
- `components/` holds search, tip, and detail UI.
- `hooks/useProgressiveTransfers.ts` loads detail pages.
- `lib/lens-explorer.ts` fetches Lens Explorer transfers.
- `lib/lens-explorer-cache.ts` stores those pages.
- `lib/transfer-source.ts` classifies a transfer.
- `lib/lens-api.ts` calls the Lens GraphQL API.
- `remotion/` holds the thank-you video.
- `sticker-library/` holds sticker images.
- `scripts/generate-thank-you-audio.mjs` writes WAV files to `public/thank-you/`.
- `public/` holds static files.

## License

This project uses the MIT license.
See `LICENSE`.
The app targets Lens Chain.
