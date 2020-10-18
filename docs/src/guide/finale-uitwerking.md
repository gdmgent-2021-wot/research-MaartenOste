# Eindresultaat

## Algemeen

Bekijk de tutorial hier: [https://youtu.be/DUM4QTjsdUw](https://youtu.be/DUM4QTjsdUw)

Mijn finale uitwerking voor big data visualization is een een kaart geworden. Op deze kaart kan de gebruiker op elk land van de wereld klikken. Door te klikken op een land zal er een nieuw menu opengaan. In dit menu is zijn vier statistieken van dat land te zien:

1. De evolutie van het aantal inwoners van dat land
2. De evolutie van de levensverwachting van een persoon
3. De evolutie van de waarde van het BBP (Bruto Binnenlands Product)
4. De evolutie van de kwaliteit van het lager onderwijs

Ik koos voor deze vier statistieken, omdat deze statistieken het beste ingevuld waren op de World Bank Open Data. Voor andere onderwerpen werd vaak maar een deel van de data ingevuld en voor sommige landen was er soms zelfs geen data.

Wanneer de data voor een bepaald land weergegeven wordt, kan de gebruiker twee kanten op. Ofwel kan hij het menu sluiten en keert hij terug naar de kaart om dan opnieuw een land te kiezen waarvan de data moet worden weergegeven. Ofwel kan de gebruiker kiezen om de data van het huidige land te vergelijken met die van een ander land. Dit kan door op vergelijk knop te klikken en dan een nieuw land te kiezen. 

## Wereldkaart

![Wereldkaart](https://drive.google.com/uc?export=view&id=1fMBSkcQvN4D8hCndmR8U0k2GTiyT0ICT)

Op de startpagina van mijn applicatie is een wereldkaart te zien. Deze werd getekend aan de hand van Plotly.js en met onderstaande code.

```js
const plotMap =() =>{
  let countryCode = [];
  let zdata= [];
  let counrtyNames= [];
  graph1 = document.getElementById('graph1');
  
  isoCountries.forEach(c =>{
    countryCode.push(iso2to3[c.ccode]);
    counrtyNames.push(c.cname);
    zdata.push(1);
  });
  
  const plotData = [{
    type: 'choropleth',
    locationmode: 'ISO-3',
    locations: countryCode,
    text: counrtyNames,
    hovertemplate: '%{text}',
    colorscale: [[0, 'rgb(53, 189, 89)'], [1, 'rgb(53, 189, 89)']],
    z: zdata,
  }];
  
  let layout = {
    geo: {
        projection: {
            type: 'robinson'
        }
    },
    font: {
      family: 'Montserrat',
    },
    margin: {
      l: 100,
      r: 10,
      b: 0,
      t: 0,
    },
  };
  Plotly.newPlot(graph1, plotData, layout)
    .then(gd => {
    gd.on('plotly_click', (d) => {
      document.getElementById('detailcontainer').style.display = 'flex';
      if (compare === false) {
        document.getElementById('detailtitle').innerText = getCounrtyName(d.points[0].location);
        firstCountry = d.points[0].location;
        plotAllData(d.points[0].location);
      } else{
        document.getElementById('detailtitle').innerText = getCounrtyName(firstCountry) + ' compared to ' + getCounrtyName(d.points[0].location);
        plotAllData(firstCountry, d.points[0].location);
      }
    });
  })
}
```

### In detail

```js
  isoCountries.forEach(c =>{
    countryCode.push(iso2to3[c.ccode]);
    counrtyNames.push(c.cname);
    zdata.push(1);
  });
```

isoCountries is een array van objecten. Deze objecten bestaan uit een ISO-2 code (ccode) en de Engelstalige naam van het land (cname). De isoCountries array wordt opgesplitst in 3 arrays met de namen, ISO-3 codes en een "data" array. In deze data array wordt voor elk land de waarde 1 toegevoegd. Deze array met waarden is verlpicht om via Plotly de wereldkaart te tekenen.

```js
  const plotData = [{
    type: 'choropleth',
    locationmode: 'ISO-3',
    locations: countryCode,
    text: counrtyNames,
    hovertemplate: '%{text}',
    colorscale: [[0, 'rgb(53, 189, 89)'], [1, 'rgb(53, 189, 89)']],
    z: zdata,
  }];
```

De drie arrays waar hierboven werd over gesproken worden hier gebruikt als data voor de kaart. Het type geeft aan dat het hier om een wereldkaart gaat. De locationmode wordt ingesteld op ISO-3, zodat Plotly weet hoe de kaart getekend moet worden. De 'text' variabele zal gebruikt worden om weer te geven wanneer men hovert over een land. Hier worden dus de volledige namen en niet de ISO codes ingestoken, zodat dit duidelijk is voor de gebruiker.

```js
let layout = {
    geo: {
        projection: {
            type: 'robinson'
        }
    },
    font: {
      family: 'Montserrat',
    },
    margin: {
      l: 100,
      r: 10,
      b: 0,
      t: 0,
    },
  };
```

Zoals de naam layout al doet vermoeden dient deze om de grafiek te stijlen. Het type robinson zorgt dat er een soort van 3D effect in de kaart zit. Het font zal gebruikt worden bij de titels en de legende van de grafiek. 

```js
  Plotly.newPlot(graph1, plotData, layout)
```

Hierna wordt de kaart getekend door bovenstaande instellingen te koppelen in de newPlot functie van Plotly.

```js
  .then(gd => {
    gd.on('plotly_click', (d) => {
      document.getElementById('detailcontainer').style.display = 'flex';
      if (compare === false) {
        document.getElementById('detailtitle').innerText = getCounrtyName(d.points[0].location);
        firstCountry = d.points[0].location;
        plotAllData(d.points[0].location);
      } else{
        document.getElementById('detailtitle').innerText = getCounrtyName(firstCountry) + ' compared to ' + getCounrtyName(d.points[0].location);
        plotAllData(firstCountry, d.points[0].location);
      }
    });
  })
```

Als laatste onderdeel van de kaart is er het deel dat het klikken op de kaart mogelijk maakt. Er wordt een click-eventlistener toegepast op de kaart. Omdat de kaart een choropleth is, bevat dit event de ISO-code van het land waarop geklikt is. Hierna zal het detailmenu van het land opengaan, waarop gekeken zal worden of de compare variabele true of false is. Afhankelijk van de waarde van compare zullen de grafieken voor één of twee landen getekend worden en zal de titel ook correct ingesteld worden.

## Detailpagina

![Detailpagina](https://drive.google.com/uc?export=view&id=1tsFtJGOMKnziA1cjqlRt8yM6nx2hnChO)

Op de detailpagina worden voor elk land vier grafieken getekend. De gebruiker kan deze vergelijken met een ander land.

### In detail

```js
const plotAllData = async (land1, land2 = null) => {
  const loading = '<div class="loading"><img src="./assets/loading.gif" alt="loading" ></div>';
  const loadinggraphs = document.getElementsByClassName('loadinggraph');
  for (let i = 0; i < loadinggraphs.length; i++) {
    loadinggraphs[i].innerHTML = loading;
  }
  plotGraph (land1, land2, 'population');
  plotGraph (land1, land2, 'lifeExpectancy');
  plotGraph (land1, land2, 'GDP');
  plotGraph (land1, land2, 'school');
}
```

Wanneer de gebruiker een land heeft aangeklikt zal de detailpagina weergegeven worden en de plotAllData functie opgeroepen worden. In afwachting van de data worden de grafieken vervangen door een gif om aan te geven aan de gebruiker dat de data wordt geladen. Daarna zullen de vier grafieken getekend worden met de plotGraph functie.

```js
const plotGraph = async (land1, land2, type) => {
  let graph = document.getElementById(graphInfo[type].htmlId);
  let temp1;
  if (sessionStorage.getItem(land1 + '.' + type)) {
    temp1 = JSON.parse(sessionStorage.getItem(land1 + '.' + type));
  } else {
    await fetchData(land1, graphInfo[type].indicator).then(data => {
      temp1 = data;
      sessionStorage.setItem(land1 + '.' + type, JSON.stringify(data));
    });
  }

  let graphLine1= {
		x: [],
    y: [],
    name: getCounrtyName(land1),
    type: 'scatter',
    line: {
      color: 'rgb(250, 95, 95)',
    }
  }

  temp1[1].forEach((el) => {
		graphLine1.x.unshift(parseInt(el.date));
		graphLine1.y.unshift(el.value);
	});

  let layout = {
    title:graphInfo[type].graphTitle,
    margin: {
      l: 40,
      r: 30,
      b: 30,
      t: 50,
    },
    font: {
      family: 'Montserrat',
    }
  };

  graph.innerHTML = '';

  if (land2 !== null) {
    let temp2;
    let graphLine2 = {
      x: [],
      y: [],
      name: getCounrtyName(land2),
      type: 'scatter',
      line: {
        color: 'rgb(10, 95, 95)',
      }
    }
    if (sessionStorage.getItem(land2 + '.' + type)) {
      temp2 = JSON.parse(sessionStorage.getItem(land2 + '.' + type));
    } else {
      await fetchData(land2, graphInfo[type].indicator).then(data => {
        temp2 = data;
        sessionStorage.setItem(land2 + '.' + type, JSON.stringify(data));
      });
    }
    temp2[1].forEach((el) => {
      graphLine2.x.unshift(parseInt(el.date));
      graphLine2.y.unshift(el.value);
    });
    Plotly.newPlot(graph, [graphLine1, graphLine2], layout);

  }else{
    Plotly.newPlot(graph, [graphLine1], layout);
  }
}
```

### plotGraph in detail

```js
  if (sessionStorage.getItem(land1 + '.' + type)) {
    temp1 = JSON.parse(sessionStorage.getItem(land1 + '.' + type));
  } else {
    await fetchData(land1, graphInfo[type].indicator).then(data => {
      temp1 = data;
      sessionStorage.setItem(land1 + '.' + type, JSON.stringify(data));
    });
  }
```

Om de API van de world bank open data zal steeds eerst gekeken worden of de data niet terug te vinden is in de session storage. Wanneer dit niet het geval is, zal de data gefetcht worden van de API en dan opgeslaan worden in de session storage. Het graphInfo object is een object dat ik zelf heb aangemaakt om per soort grafiek bij te houden wat de indicator voor de fetch is, de titel voor de grafiek en het id van het html element waarin deze getekend moet worden.

```js
const graphInfo = {
	'population' :{
		indicator: 'SP.POP.TOTL',
		htmlId: 'graphPopulation',
		graphTitle: 'Evolution of the population',
	},
	'lifeExpectancy':{
		indicator: 'SP.DYN.LE00.IN',
		htmlId: 'graphLifeExpectancy',
		graphTitle: 'Evolution of life expectancy',
	},
	'GDP':{
		indicator: 'NY.GDP.MKTP.CD',
		htmlId: 'graphGDP',
		graphTitle: 'Evolution of GDP (in $)',
	},
	'school':{
		indicator: 'SE.PRM.ENRR',
		htmlId: 'graphSchool',
		graphTitle: 'Evolution of Level of Education in Primary School',
	}
}
```


```js
const fetchData = async (land, indicator) =>{
  let data = await fetch(`https://api.worldbank.org/v2/country/${land}/indicator/${indicator}?format=json`);
	return data.json();
}
```

De fetchData functie zal de data uit de world bank open data API fetchen en teruggeven als een JSON.

```js
  let graphLine1= {
		x: [],
    y: [],
    name: getCounrtyName(land1),
    type: 'scatter',
    line: {
      color: 'rgb(250, 95, 95)',
    }
  }

  temp1[1].forEach((el) => {
		graphLine1.x.unshift(parseInt(el.date));
		graphLine1.y.unshift(el.value);
	});
```

Net als bij de wereldkaart moet er een object gemaakt worden voor de data, alleen is het type hier nie choropleth, maar scatter. Deze wordt dan daarna gevuld in de foreach loop.

```js
  if (land2 !== null) {
    let temp2;
    let graphLine2 = {
      x: [],
      y: [],
      name: getCounrtyName(land2),
      type: 'scatter',
      line: {
        color: 'rgb(10, 95, 95)',
      }
    }
    if (sessionStorage.getItem(land2 + '.' + type)) {
      temp2 = JSON.parse(sessionStorage.getItem(land2 + '.' + type));
    } else {
      await fetchData(land2, graphInfo[type].indicator).then(data => {
        temp2 = data;
        sessionStorage.setItem(land2 + '.' + type, JSON.stringify(data));
      });
    }
    temp2[1].forEach((el) => {
      graphLine2.x.unshift(parseInt(el.date));
      graphLine2.y.unshift(el.value);
    });
    Plotly.newPlot(graph, [graphLine1, graphLine2], layout);

  }else{
    Plotly.newPlot(graph, [graphLine1], layout);
}
```

Uiteindelijk wordt er gekeken of het tweede land is ingevuld of niet. Indien het ingevuld is wordt er nog een data object gemaakt en dan worden beide objecten toegevoegd aan de newPlot functie.