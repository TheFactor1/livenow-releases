# League filter, feed picker and player-open transition: motion spec

Condensed from the two interactive mockups (league filter, feed picker). CSS px ≈ dp at phone scale; the mockup phone is 316dp wide.

## Tokens
- Tickets: navy #1B2233, navy-2 #232B3E (sheet/tray bg), line #36405A, paper/gold #CDBB8A, cream #F1E8D8, ink #1B2233, red #E0563F, red-ink #9A2B1C, dim #8A93A8, off (dashed) #4A5470. Display font = the app's Big Shoulders (mockup used Bebas), mono = JetBrains Mono.
- Mercury: ground #E8EBE6, card #FFFFFF, ink #111111, red #D9342B, dim #6C706B, off #D3D7D0. Bricolage Grotesque + IBM Plex Mono.

## Easing
- tk-ease = cubic-bezier(.2,.8,.2,1). "pop" = cubic-bezier(.2,1.6,.4,1) (overshoot).
- Springs (mass 1): firm k170 c16 (Compose dampingRatio = c/(2*sqrt k) = 0.61), soft k110 c11 (0.52). Custom k/c values below convert the same way.
- Reduced motion: everything snaps (no springs, 0 durations); the player open becomes a plain fade.

## Goo (Mercury)
SVG filter: GaussianBlur stdDev 8, then alpha matrix a' = 26*a - 11 (steep threshold ~0.42), composite SourceGraphic atop. Smaller variant: blur 7, 22a - 9.
Android: draw all black blobs of one group into one layer; RenderEffect.createBlurEffect(8dp) chained with a ColorMatrixColorFilter whose alpha row is [0,0,0,26,-11*255]. API < 31: no blur, plain rounded shapes.

## League filter

### Tickets A "Stub tray" (inline under the tab row)
- Tab row stays; a 40dp "tune" (sliders icon) button at its right. Expanded = paper fill, ink icon.
- Tray height 0 → content, 420ms tk-ease. Bg navy-2, padding 12/14/14, gap 10, bottom border 1.5dp dashed line colour.
- Order: label "SORT" (mono 600 ~8.6sp, letter-spacing .16em, dim); sort split ticket; label row "LEAGUES SHOWN … 3 of 10" (right side cream); 4-column stub grid (gap 6 v / 7 h); preset row; note line.
- Sort split ticket: two equal halves on paper, r5, dashed 1.5dp divider rgba(ink,.4); 12dp navy-2 semicircle notches top and bottom at the centre. Half: display ~17sp ink, min-h 40, alpha .5 unselected → 1 selected; leading 9dp dashed-outline dot, selected = filled navy and scale 1.25 (300ms pop).
- Stub: paper bg, 1.5dp paper border, r4, min-h 44, padding 7/2/6; 8dp navy-2 half-circle notches on the left and right edges at mid-height. Line 1: league abbr display ~16sp with a 6dp league-colour dot before it. Line 2: "N tonight" / "none", mono 7.4sp caps, alpha .75. OFF (voided) = transparent bg, dashed #4A5470 border, dim text, abbr struck through. Press scale .92 (400ms pop); colours 200ms.
- Presets: three equal buttons "Show all" / "My leagues" / "Save as mine": mono 700 ~9sp caps, 1.5dp paper border, r4, min-h 36, cream text; active (current preset matches) = paper fill, ink text. Save → note "Saved. My leagues: NHL, NBA…", otherwise "My leagues: …" (mono 8sp caps dim).
- Game tickets enter: fade + translateY 7dp, 380ms tk-ease, 24ms stagger.

### Mercury D3 "Droplets, inside the header"
- No tab row. Header: title "Tonight" (800, ~34sp, letter-spacing -.035em) + 10dp gap + league pill; right side: live count (red) + date · N games (mono 9.6sp dim, right aligned).
- Pill: black, white text, h32 r16, padding 0 12, Bricolage 600 12.5sp, label "All" or the league, trailing chevron (6dp, 1.6dp stroke, 45°). Press scale .92 on the firm spring. When the label changes: "squish" scale(.82,1.08) → 1 over ~700ms firm spring. Tap = squish + open sheet + droplets spill.
- Sheet: white, top radius 28, slides up on the soft spring, scrim rgba(17,17,17,.28). Top row "Leagues" (800, 21.6sp) + Done. Then: label "VIEWING" + lava tab row (the per-league view chooser now lives in the sheet); label "SORT" + slosh switch; droplet pool; links All / My leagues / Save as mine + note.
- Lava tab row: black blob behind the selected tab (h34 r17) under goo, plus a lagging satellite drop (size .62 × 34) that trails like a lava lamp. Leading edge spring k320 c26, trailing edge k110 c14, so it stretches and thins: height = base / sqrt(stretch), clamped 0.5–1×; skewX = clamp(-velocity*.01, -12°, 12°). Satellite x k45 c7, y k60 c7 with a +150 velocity kick each move. Selected text white (350ms).
- Slosh switch (Sort): track h42 r21 ground colour; black blob inset 4dp inside the chosen half using the same edge springs (no satellite); a label turns white when its centre is inside the blob.
- Droplet pool: shown leagues = black pills in a 4-col grid (gap 4, h40, width (W-12)/4), top offset 20; they touch and merge through the goo so they read as one pool. Hidden leagues = 22dp drops on a 3-col shelf below (shelf top = 20 + rows*44 + 24; row step 34; x = col*(W/3) + 13), labelled "HIDDEN · TAP TO POUR BACK"; above the pool "SHOWN · N · TAP TO DRIP OFF". Drop x/y spring k140 c13, w/h k220 c19; label width = w + 62*o where o (0 shown → 1 hidden) springs k180 c18, so hidden drops have their text beside them. Each drop has an 18dp satellite following on a soft spring k55 c8, which makes the stringy split and merge. Pool height animates on the firm spring. Label white when shown, ink when hidden (350ms).
- Spill on open: every drop starts at the pill position (sheet coords ≈ (40,-60)), size 14, random x velocity ±130, upward velocity -(120..280), then springs home, 28ms stagger.

## Feed picker
Shared sheet head: away/home 28dp abbr circles overlapping -7, meta "AHL · Puck drop 7:05 PM · 2 of 4 live", title "Syracuse Crunch @ Rochester Americans", sub "Choose a feed". Rows show HOME/AWAY tag, name, and "Source · quality" as small text only. Reminder toast: "Reminder set. We'll start the <feed> when it opens, around 7:05 PM." / "Reminder off for the <feed>."

### Tickets B "Pick your seat"
- Seat map h178: rink rectangle inset 64 left/right, 40 top/bottom, 1.5dp line-colour border r30, red centre line rgba(224,86,63,.55), blue lines at 33% / 66% in line colour, 30dp centre circle.
- Seats (paper r4, display ~15sp + mono 6.7sp caps sub "Live" / "7:05"): AWAY end = left column (x 0, w56, top 40 to bottom 40); HOME end = right column; Camera 1 = top centre (w88 h32); Camera 2 = bottom right (right 64, w80 h32). Teams by abbr, cameras "CAM 1/2". Not open: transparent, dashed #4A5470, cream text. Selected: 2dp navy-2 ring + 2dp red ring. Press .94 (350ms pop).
- Legend: "Live now" (paper square) / "Opens around 7:05 PM" (dashed square).
- Printer slot: 5dp bar #0E1320 r3. The ticket prints out of it: translateY -105% → 0, 550ms tk-ease; on a new selection the old one retracts, then 200ms later the new one prints.
- Printed ticket: columns [1fr | 82dp], paper (cream if not open), bottom radius 6, min-h 92, 12dp notch at the divider bottom. Left: "SEC HOME · LIVE NOW" (mono 7.4sp caps .75), name (display 19sp), "FloSports · 1080p · note" (mono 7.4sp caps .7). Right (dashed divider): live = navy "ADMIT" + small "WATCH NOW"; not open = outlined "HOLD SEAT" + "OPENS 7:05" (red-ink); held = red-ink fill "HELD" + "TAP TO RELEASE".
- Admit: punch hole (14dp navy-2 circle, top right of the stub, scale 0 → 1, 300ms pop), then 260ms later the Tickets transition starts and the sheet closes.

### Mercury A "Droplets"
- Rows: [60dp | 1fr], min-h 64. Live feed = 46dp black drop (under goo) with a white play triangle; consecutive live drops joined by a 12dp black strand (r6) between drop centres (length on spring k120 c12). Group labels "LIVE NOW · 2" (red with dot) and "OPENS AROUND 7:05 PM · 2".
- Not-open feed: 40dp ring, 2dp #D3D7D0 border, white inside, black liquid at 22% height with a wavy top (radius 46% 54% 0 0 / 12 14 0 0) sloshing (translateX 12% + rotate -4°, 3.2s ease-in-out, alternate, infinite). Tap = reminder on: level rises to 58% on the soft spring (~1.1s); meta becomes "Reminder on · opens around 7:05 PM".
- Sheet open: drops pour in (size 0 → 46, spring k190 c11, delays 120ms + i*140ms); the strand grows in at 380ms.
- Tap a live feed: drop swells to 60 (k190 c11), strands retract to 0, after 160ms the drop hides and the Mercury transition starts from it; sheet closes.

## Player-open transitions (one per look, used everywhere the player opens)

### Mercury "pour"
One black blob under goo whose rect is driven by four edge springs from the source rect (drop, Watch pill, alert, mini player dock) to the target rect (video area).
- Left/right edges k150 c15. Going up: top edge stiff k260 c22, bottom edge loose k85 c10 (swap when going down), so it stretches into a teardrop and pours in.
- Corner radius = min(28dp, w/2, h/2).
- Two trailing drips: sizes z = min(20, source width × .45) and z × .7; they start at the source centre and spring to just inside the target's far edge (target bottom − 10 when going up) with k55 c9 and k34 c7; their size springs to 0 (k26 c8).
- At 640–680ms the player shows (video area starts dark with "Starting feed" and a 1.1s loading bar); 160ms later the blob fades out over 300ms. Player fades in over 250ms. Video box r28.

### Tickets "punch and enter"
After the punch (300ms pop) + 260ms, a paper card reading "ADMIT ONE" (display ~24sp, letter-spacing .1em, ink) flies from the ticket's rect to the video rect grown 6dp on every side (the player's 6dp paper frame), animating position and size over 520ms tk-ease. At 540ms the player shows and the card fades out over 300ms. Tickets video box: r4 with a 6dp paper ring.
From Watch, alerts or the mini player there is no printed ticket: punch the tapped element (Watch stub / mini player) and fly from its rect.

## Rules
No scores of the watched game anywhere in these screens or transitions.
