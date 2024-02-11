<%*
// ソート用関数
function sorter(toOrdered, order = "asc"){
	return (a, b) =>
		order == "asc"
			? toOrdered(a) > toOrdered(b)
			? 1 
			: toOrdered(b) > toOrdered(a)
			? -1 
			: 0 
		: toOrdered(a) < toOrdered(b) 
		? 1 
		: toOrdered(b) < toOrdered(a) 
		? -1 
		: 0;
};
// 試合情報の入力
const compe = await tp.system.prompt(`大会名とセクション[ex.天皇杯2回戦, J1第1節]`);
const place = await tp.system.prompt(`場所`);
const homeTeam = await tp.system.prompt (`ホームチーム名`);
const awayTeam = await tp.system.prompt (`アウェーチーム名`);

// 日付の取得
const date = tp.date.now(`YYYY-MM-DD`)
const year = tp.date.now(`YY`)

// 通し番号の作成
const maxNumber = Number(
	app.vault.getMarkdownFiles()
		.filter((x) => x.name.startsWith(`${year}-`))
		.sort(sorter((x) => x.name))
		.pop()
		?.name.split(" ")[0]
		.replace(`${year}-`, "") ?? 0
	);
const newNumber = `000${maxNumber + 1}`.slice(-3);

//ファイルの作成
const title = `${year}-${newNumber} ${compe}${homeTeam}vs${awayTeam}`; 
const filePath = `soccer/${title}.md`;
if(await tp.file.exists(filePath)){
	new Nortice(`Error: ${filePath} is already existed.`);
	return;
}

// ファイルの内容
const noteBody =`---
create: ${date}
last modified: ${date}
location: (geoコード)
place: ${place}
weather: (気象情報)
tags: (必要なタグ)
---
## 試合前

## 前半

## 後半

## 試合後

`;

await app.vault.create(filePath, noteBody)
await app.workspace.openLinkText("", filePath, true)
%>