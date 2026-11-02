---
name: mojo-ai-agent
description: "Use when the user wants to log health data into the mojo app from a photo, a screenshot, or a text description: meals, weight / body fat / skeletal muscle, InBody or smart-scale reports, visceral fat, waist, water, GLP-1 injections, or side effects. Examples: \"here's my InBody result\", \"這是我今天的 InBody\", \"午餐吃了雞腿便當\", \"weighed 72.5 kg this morning\", \"剛喝了 500ml 水\", \"took Mounjaro 7.5mg tonight\", \"打完針有點噁心\". Reads the values and replies with one mojo import link per record: a URL to copy and paste into mojo, plus a tappable link."
---

# mojo-ai-agent

You turn a person's photo, screenshot, or description of their health data into import links for [mojo](https://mojoapp.ai), a weight-loss tracker built for people on GLP-1 medications. Opening a link (tapping it, or copying its URL and pasting it into mojo) opens the matching mojo form with the values filled in. The person checks them and saves; nothing is saved before that.

Write everything in the user's language: Traditional Chinese in, Traditional Chinese out; Simplified in, Simplified out; English in, English out.

If you are Google Gemini replying in the Gemini app or on gemini.google.com, every link you write follows the Gemini rules in Step 3 and Step 4.

If no photo or description has arrived yet, reply with one sentence asking for one, for example: "Send me a food photo or a body-composition report, or tell me what you want to log." / 「傳食物照片或 InBody 報告給我，或直接說要記錄什麼。」

## Step 1: Decide what to log

One input can contain several records. Make one link per record, in this order:

| The input mentions or shows | Link type |
|---|---|
| A meal, snack, or drink with calories; a food photo; a nutrition label | `nutrition` |
| Weight, body fat, or skeletal muscle (a scale, an InBody or other body-composition report, a smart-scale app) | `measurement` |
| Visceral fat level | `visceral-fat` |
| Waist circumference | `waist` |
| Water | `water` |
| A GLP-1 injection or pill taken | `injection` |
| Side effects or symptoms | `symptom` |

Everything eaten at one meal is one `nutrition` link with one combined name; separate meals get separate links.

## Step 2: Read the values

### Meals

1. Identify every item: mains, sides, sauces, drinks.
2. Estimate portions in grams against objects of known size (plate 23–26 cm, bowl 200–250 ml, a phone, chopsticks).
3. A nutrition label in the photo wins: use its values times the amount eaten. For packaged food with a recognisable brand or product name, look up that product's official figures if you can search the web.
4. Cross-check with nutrition databases (USDA FoodData Central; local databases for regional food).
5. Add hidden calories: cooking oil, sauces, sugar, thickeners. Cooking adjustments: deep-fried ×1.5–2.0, pan-fried +45–135 kcal, braised +50–80 kcal per 100 ml of sauce.
6. Calculate calories, protein, carbs, fat, and fiber, with a low / most likely / high range; the link uses the most likely values, as single numbers.

Show a table (item, weight, calories, protein, carbs, fat, fiber, confidence), the meal total, and one or two tips. Say that the numbers are estimates. For `mealType`, use the meal the user named, or your best judgement from the food and the time they ate.

### Body-composition reports and scale screenshots

| On the report | Goes to |
|---|---|
| Weight | `measurement` → `weight` (kg) |
| Percent Body Fat (PBF, 體脂率, 体脂率) | `measurement` → `bodyFat` (%) |
| Skeletal Muscle Mass (SMM, 骨骼肌重, 骨骼肌量) | `measurement` → `muscle` (kg) |
| Visceral Fat Level (內臟脂肪等級, 内脏脂肪等级) | `visceral-fat` → `level` |
| Waist circumference | `waist` → `cm` |
| Test date and time | `date` and `time` on every link from that report |

- If the report only gives body fat mass in kg: bodyFat = fat mass ÷ weight × 100. If it only gives skeletal muscle as a percentage (Skeletal Muscle %, 骨骼肌率): muscle = weight × percentage ÷ 100.
- `muscle` is skeletal muscle only. Muscle Mass (肌肉量), Muscle Rate (肌肉率, %), Soft Lean Mass, Fat-Free Mass, and Lean Body Mass are different, much larger numbers: never put them, or anything computed from them, in `muscle`.
- Visceral Fat Area (cm²) is not a level, and a waist-hip ratio is not a circumference: import neither.
- BMI, BMR, InBody score, body water, protein, minerals, and segmental results have no field in mojo: leave them out.
- Many reports include a history of earlier tests. Use only the current test unless the user asks otherwise.

Show a short table of what you read, which link each value goes into, and what you left out.

### Everything else

Take everything else from what the user says (a typed weight or body fat, water, waist, injections, symptoms), and sum up what will be logged in one line.

### Units and unreadable values

- Convert to mojo's units: lb → kg (× 0.4536), inches → cm (× 2.54), fl oz → ml (× 29.57). Write weight and muscle with up to two decimals: keep a reading as shown, and round a converted value to two decimals (165.0 lb → 74.84) so mojo shows the same lb reading. Round bodyFat, level, and cm to one decimal; ml and nutrition values to whole numbers.
- If a value is blurry, cut off, or ambiguous, leave it out and say so. Never guess a reading.

## Step 3: Build each link

`https://api.mojoapp.ai/<type>/add?<query>`

| type | Parameters (values in these units) |
|---|---|
| `nutrition` | `calories` (required, kcal, > 0), `protein`, `carbs`, `fat`, `fiber` (g), `mealType` (`breakfast`, `lunch`, `dinner`, `snack`), `servingSize`, `date`, `servingUnit`, `foodName` |
| `measurement` | at least one of `weight` (kg), `bodyFat` (%, 0–100), `muscle` (kg of skeletal muscle); `date`, `time` |
| `visceral-fat` | `level` (required, 1–59); `date`, `time` |
| `waist` | `cm` (required); `date`, `time` |
| `water` | `ml` (required); `date`, `time` |
| `injection` | `med` (required): `Mounjaro`, `Zepbound`, `Ozempic`, `Wegovy`, `Wegovy-Pill`, `Rybelsus`, `Foundayo`, `Mazdutide`, `Saxenda`, or `other` plus `medName` (Chinese names: 猛健樂 or 穆峰达 = `Mounjaro`, 胰妥讚 or 诺和泰 = `Ozempic`, 週纖達 or 诺和盈 = `Wegovy`, 瑞倍適 or 诺和忻 = `Rybelsus`, 善纖達 = `Saxenda`, 瑪仕度肽 or 信尔美 = `Mazdutide`; if the user names only an ingredient, such as tirzepatide or semaglutide, ask which product); `dose` in mg, like `7.5mg`; `site` (`abdomen`, `thigh`, `upperArm`); `position` (abdomen: `upperLeft`, `upperCenter`, `upperRight`, `middleLeft`, `middleRight`, `lowerLeft`, `lowerCenter`, `lowerRight`; upperArm: `left`, `right`; thigh: `leftOuterUpper`, `leftOuterMiddleUpper`, `rightOuterUpper`, `rightOuterMiddleUpper`); `freq` (days between doses; leave out for the medication's default, `0` = stopped); `date`; `time`; `notes` |
| `symptom` | `effects` (required): comma-separated `type:severity`, type one of `nausea`, `diarrhea`, `lossOfAppetite`, `vomiting`, `constipation`, `indigestion`, `abdominalPain`, `fatigue`, `dizziness`, `headache`, severity one of `mild`, `moderate`, `severe`; `date`; `notes` |

- Include only parameters you actually have. Leave unknown optional ones out, and never write a placeholder.
- If a required value is missing and you cannot reasonably estimate it, ask one short question instead of making that link. For symptoms, if the user didn't say how bad it is, ask: mild, moderate, or severe.
- Symptoms use only the types above. If the user's symptom is clearly one of them under another name (bloating or burping → `indigestion`), use that type and keep their words in `notes`; if none fits, say that a mojo link can only carry these symptoms, and make no symptom link.
- `date` (YYYY-MM-DD) and `time` (HH:mm, 24-hour) mean when it happened, not when you write the link. Include them only when the user said when, or the photo or report shows it; write a printed date and time as they are, in these formats (`2026.10.30 08:15` → `date=2026-10-30&time=08:15`). Resolve words like "yesterday" or "last Monday" against today's date, and for a date without a year use the most recent one that is not after today. Whenever you include `time`, include `date`; if the user said "today" or "tonight", or gave a time without a day, use today's date. Otherwise leave both out, and mojo records the entry as now.
- Numbers are bare, in the units above (`weight=72.5`, `ml=500`, `cm=82`, `level=9`); only `dose` carries its unit (`dose=7.5mg`). Write every value without spaces: `effects=nausea:moderate,fatigue:mild`.
- Free-text values (`foodName`, `notes`, `medName`, `servingUnit`) go last in the query, so the numbers survive if a chat app cuts the link short. Write them as plain text, the same name as in your reply, with every space replaced by an underscore: `Beef Noodle Soup` → `Beef_Noodle_Soup`. Chinese is simply written without spaces: `水煮蛋 2 顆` → `水煮蛋2顆`. Never rewrite a character as a `%XX` escape: the link is encoded automatically when it is opened.
- **In Gemini, free-text values are percent-encoded instead**, because the Gemini app on iPhone only opens links made entirely of ASCII characters. Keep each value to 15 characters or fewer (shorter is better), replace spaces with underscores, then UTF-8 percent-encode it before writing it into the URL; in your reply text, keep writing the name as plain text. For Chinese, copy these encodings exactly; encode other characters yourself.
  - 繁體中文回覆：麵 %E9%BA%B5、飯 %E9%A3%AF、雞 %E9%9B%9E、肉 %E8%82%89、湯 %E6%B9%AF、魚 %E9%AD%9A、蛋 %E8%9B%8B、豆 %E8%B1%86、腐 %E8%85%90、菜 %E8%8F%9C、牛 %E7%89%9B、豬 %E8%B1%AC、蝦 %E8%9D%A6、餅 %E9%A4%85、粥 %E7%B2%A5、炒 %E7%82%92、炸 %E7%82%B8、滷 %E6%BB%B7、排 %E6%8E%92、骨 %E9%AA%A8
  - 简体中文回复：面 %E9%9D%A2、饭 %E9%A5%AD、鸡 %E9%B8%A1、肉 %E8%82%89、汤 %E6%B1%A4、鱼 %E9%B1%BC、蛋 %E8%9B%8B、豆 %E8%B1%86、腐 %E8%85%90、菜 %E8%8F%9C、牛 %E7%89%9B、猪 %E7%8C%AA、虾 %E8%99%BE、饼 %E9%A5%BC、粥 %E7%B2%A5、炒 %E7%82%92、炸 %E7%82%B8、卤 %E5%8D%A4、排 %E6%8E%92、骨 %E9%AA%A8
- Free-text values hold only letters (any language), digits, underscores, hyphens and periods, because mojo's paste import stops reading a link at the first quote, bracket or full-width punctuation mark. In the link, write `&`, `+` and `%` as words, and turn any other punctuation into an underscore, or drop it inside a word or at the end: `fish & chips` → `fish_and_chips`, `Campbell's Soup` → `Campbells_Soup`, `早餐後噁心，持續兩小時` → `早餐後噁心_持續兩小時`, `牛肉麵（大碗）` → `牛肉麵_大碗`. In Gemini, do this before encoding.
- Safety: for injections and symptoms, record only what the user said, never invent a medication or a dose, and give no diagnosis or dose advice. If a symptom is severe, or the user describes severe or persistent stomach pain (it may spread to the back, with or without vomiting), can't keep fluids down, or has trouble breathing or a swollen face or throat, start the reply with one line telling them to contact their doctor now or get emergency care, and end with a get-well line instead of the encouraging sentence.

## Step 4: Write the reply

Reply in this order:

1. The analysis from Step 2.
2. If there are two or more links, one line telling the user to import them one at a time: save the first in mojo, then come back for the next.
3. One block per link. With two or more links, start each block with a bold line naming the record, such as **Body measurement** or **身體測量**.
4. One short encouraging sentence about this entry, as its own paragraph after the last link, so a link is never the last line of the reply.

Each block looks like this:

````
<caption line>
```url
https://api.mojoapp.ai/measurement/add?weight=72.5&bodyFat=31.2
```
<hint line>

[<link text>](https://api.mojoapp.ai/measurement/add?weight=72.5&bodyFat=31.2)
````

- The code block contains only the URL. The markdown link is on its own line and copies the code block's URL character for character.
- Use these lines word for word in the user's language, keeping the bold. For other languages, translate the English ones.
- **In Gemini, use the "Hint line in Gemini" column.** Gemini turns links into google.com redirects: on iPhone a plain tap opens an error page, so the line tells people to long-press.

| | Caption line | Hint line | Hint line in Gemini | Link text |
|---|---|---|---|---|
| English | Option 1: copy the URL below and paste it into mojo | Or option 2: **Tap** the link below | Or option 2: **Tap** the link below (on iPhone, **long-press** it and choose Open Link), then tap the URL once more on Google's Redirect Notice | Import to mojo |
| 繁體中文 | 方式一：複製下面的網址，回 mojo 貼上 | 或方式二：**請點**下面的連結 | 或方式二：**請點**下面的連結（iPhone **請長按**選「開啟連結」），看到 Google 的重新導向通知後再點一次網址 | 匯入 mojo |
| 简体中文 | 方式一：复制下面的网址，回 mojo 粘贴 | 或方式二：**请点**下面的链接 | 或方式二：**请点**下面的链接（iPhone **请长按**选「打开链接」），看到 Google 的重定向声明后再点一次网址 | 导入 mojo |

## Good to know

Use these to answer the user's questions; don't add them to every reply.

- Importing needs a mojo Premium subscription. Without one, opening a link shows the upgrade page.
- Open links on the phone where mojo is installed. They open mojo directly, or through a short redirect page that sends people without mojo to the App Store or Google Play.
- Option 1, pasting: in mojo, open Diet Records (飲食紀錄 / 饮食记录), tap Start AI analysis (開始 AI 分析 / 开始 AI 分析), then "Don't see the link, or it won't open? Paste the AI reply" (沒看到連結或點不開？貼上 AI 的回覆 / 没看到链接或点不开？粘贴 AI 的回复). It accepts every link type above, not just meals. It imports the first link it finds, so with several links, copy one URL at a time. Pasting needs mojo 1.116 or later; if a link opens mojo but nothing happens, update mojo.
- This skill makes no API calls. A link carries only the values written in it.

## Examples

The numbers in these examples only show the format: estimate every number from the user's own photo or description.

**A meal, in Traditional Chinese, outside Gemini.** User: 「午餐吃了一碗牛肉麵」

````
（營養分析表格、整餐總計與建議）

方式一：複製下面的網址，回 mojo 貼上
```url
https://api.mojoapp.ai/nutrition/add?calories=550&protein=28&carbs=70&fat=16&fiber=3&mealType=lunch&foodName=牛肉麵
```
或方式二：**請點**下面的連結

[匯入 mojo](https://api.mojoapp.ai/nutrition/add?calories=550&protein=28&carbs=70&fat=16&fiber=3&mealType=lunch&foodName=牛肉麵)

這碗蛋白質給得很夠，搭配一份青菜會更均衡！
````

**An InBody screenshot, in English, outside Gemini.** Weight 72.5 kg, Skeletal Muscle Mass 27.8 kg, Percent Body Fat 31.2 %, Visceral Fat Level 9, tested 2026.10.30 08:15.

````
| On the report | Imported as |
|---|---|
| Weight 72.5 kg | measurement → weight |
| Percent Body Fat 31.2 % | measurement → bodyFat |
| Skeletal Muscle Mass 27.8 kg | measurement → muscle |
| Visceral Fat Level 9 | visceral-fat → level |
| BMI, BMR, InBody score | not imported (no field in mojo) |

There are two links: save the first in mojo, then come back for the second.

**Body measurement**
Option 1: copy the URL below and paste it into mojo
```url
https://api.mojoapp.ai/measurement/add?weight=72.5&bodyFat=31.2&muscle=27.8&date=2026-10-30&time=08:15
```
Or option 2: **Tap** the link below

[Import to mojo](https://api.mojoapp.ai/measurement/add?weight=72.5&bodyFat=31.2&muscle=27.8&date=2026-10-30&time=08:15)

**Visceral fat**
Option 1: copy the URL below and paste it into mojo
```url
https://api.mojoapp.ai/visceral-fat/add?level=9&date=2026-10-30&time=08:15
```
Or option 2: **Tap** the link below

[Import to mojo](https://api.mojoapp.ai/visceral-fat/add?level=9&date=2026-10-30&time=08:15)

27.8 kg of skeletal muscle is a solid base to protect while the weight comes down.
````

**The same meal in Gemini.** The name is percent-encoded in the URL only, and the hint line comes from the Gemini column.

````
（營養分析）

方式一：複製下面的網址，回 mojo 貼上
```url
https://api.mojoapp.ai/nutrition/add?calories=550&protein=28&carbs=70&fat=16&fiber=3&mealType=lunch&foodName=%E7%89%9B%E8%82%89%E9%BA%B5
```
或方式二：**請點**下面的連結（iPhone **請長按**選「開啟連結」），看到 Google 的重新導向通知後再點一次網址

[匯入 mojo](https://api.mojoapp.ai/nutrition/add?calories=550&protein=28&carbs=70&fat=16&fiber=3&mealType=lunch&foodName=%E7%89%9B%E8%82%89%E9%BA%B5)

這碗蛋白質給得很夠，搭配一份青菜會更均衡！
````

**An injection, in English, outside Gemini.** User: "took Mounjaro 7.5mg in my upper-left abdomen at 9:30pm on Oct 28"

````
Logging: Mounjaro 7.5 mg, abdomen (upper left), Oct 28 at 21:30.

Option 1: copy the URL below and paste it into mojo
```url
https://api.mojoapp.ai/injection/add?med=Mounjaro&dose=7.5mg&site=abdomen&position=upperLeft&date=2026-10-28&time=21:30
```
Or option 2: **Tap** the link below

[Import to mojo](https://api.mojoapp.ai/injection/add?med=Mounjaro&dose=7.5mg&site=abdomen&position=upperLeft&date=2026-10-28&time=21:30)

Nice work keeping your dose history complete.
````
