# Mijn ervaring

## Plotly.js in React

De data die ik downloadde als json bestand van [World Bank Open Data](https://data.worldbank.org/) wou ik graag weergeven in een React app. Dit zou vrij eenvoudig verlopen. Door mijn data in de "src" map van mijn React app moest er enkel nog een *require()* operatie doen om mijn json aan te kunnen spreken in mijn app. Daarna werd de data gelogd om te zien of de data correct werd ingeladen, wat het geval was.

```js
  const data = require('./data/HIV.json');
  console.log(data);
```
![All data react](https://drive.google.com/uc?export=view&id=17B0oQfJRHOYJanyQdjQVLd1bPMs3h8nK)

Mijn volgende stap was om dit groot object specifieker en overzichtelijker te maken. Ik koos ervoor om de mensen die stierven aan HIV in België weer te geven. Aangezien er per jaar twee *values* werden bijgehouden (aantal mensen die stierven en aantal mensen die stierven per 1000 inwoners) hield ik een frequentietabel bij zodat enkel de eerste waarde van elk jaar gebruikt werd.

```js
let yearCount = [];
  data.fact.forEach((el)=>{
    if (el.dims.COUNTRY === 'Belgium') {
      if (!yearCount.includes(el.dims.YEAR)) {
        yearCount.push(el.dims.YEAR);
        let afk = el.Value.indexOf('[');
        afk-=1;
        const val = el.Value.substr(0, afk);
        console.log(el.dims.YEAR + ' : ' + val);
      }
    }
  });
```
![Clean data react](https://drive.google.com/uc?export=view&id=1NITAwbt1Lp3s2Kt0e7-4j-uyRTKARoPZ)

Daarna zou ik deze data gaan weergeven via Plotly.js. Dit werd echter geen succes. De Plotly.js package installeren via npm vormt geen enkel probleem, maar wanneer Plotly dan geïmport wordt in de react app krijg ik deze error te zien bij het compilen: *FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory*

![Error react](https://drive.google.com/uc?export=view&id=1Y5EonVRQAMZ7EMf2xCYgmYmexll96mYZ)

Het is mij helaas niet gelukt deze error op te lossen, daarom besloot ik het simpeler aan te pakken.

## Back to the basics

Ik besloot om opnieuw te beginnen met een simpele html, css en js file. Plotly.js toevoegen aan de body van de html file: 

```html
	<div class="graph" id="graph" ></div>
	<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
```

Eerst en vooral plotte ik een simpele grafiek om na te gaan of Plotly.js hier wel werkte, of ik op zoek moest naar een nieuwe data visualisatie software. Dit deed ik door onderstaande code toe te voegen aan mijn js bestand.

```js
const line1 = {
	x: [1, 2, 3, 4],
	y: [10, 15, 13, 17],
	type: 'scatter'
};

const line2 = {
	x: [1, 2, 3, 4],
	y: [16, 5, 11, 9],
	type: 'scatter'
};

Plotly.newPlot( graph, [line1, line2]);
```
![Eerste grafiek](https://drive.google.com/uc?export=view&id=1r5JhBm1C3_ymjA6DQFbkAZ9pORwlejSN)

Eenmaal dit gelukt was, ging ik mijn data toevoegen. Ondertussen vond ik dat de [World Bank Open Data](https://data.worldbank.org/) ook een vrij te gebruiken api ter beschikking stelt. Daardoor moest ik niet meer een json bestand gaan downloaden telkens ik andere data wou. 

Ik hergebruikte een deel van de code van de eerste grafiek om mijn data die ik via de fetch verkreeg weer te geven.

```js
const getData = async () => {
	let population = await fetch('http://api.worldbank.org/v2/country/be/indicator/SP.POP.TOTL?format=json');
	return population.json();
}

const plotData = async () => {
	let temp;
	await getData().then(data => {
		temp = data;
	});
	console.log(temp);
	let graphLine = {
		x: [],
		y: [],
		type: 'scatter'
	}

	temp[1].forEach((el) => {
		graphLine.x.unshift(parseInt(el.date));
		graphLine.y.unshift(el.value);
	});

	Plotly.newPlot(graph, [graphLine]);
}

plotData();

```
![Populatie belgië](https://drive.google.com/uc?export=view&id=1UxIT216Alp3nshEY-dcIhdExaarPf9In)

Daarna heb ik een inputveld en een knop toegevoegd aan mijn html en de js code wat aangepast zodat ik van elk land ter wereld de evolutie van de populatie kan zien op basis van de landcode (bv: België = be, Frankrijk = fr ...).

```html
	<input type="text" name="land" id="landinput">
	<button type="submit" id="zoek">zoek</button>
```

```js
const getData = async (land) => {
  let population = await fetch(`http://api.worldbank.org/v2/country/${land}
  /indicator/SP.POP.TOTL?format=json`);
	return population.json();
}

document.getElementById('zoek').addEventListener('click', () => {
  const land = document.getElementById('landinput').value;
  plotData(land);
});

```
![Populatie met knop](https://drive.google.com/uc?export=view&id=19mSOX1G5zDMBkJE-Bb0Fax01YV82zvOZ)

Mijn einddoel is om tegen volgende les via onderstaande kaart per land data weer te geven. Hiervoor zal ik nog wat meer onderzoek moeten doen.

![Populatie met knop](https://drive.google.com/uc?export=view&id=1XMl6HgmyUh7ZC87WJLb1nMPvlseymecC)
