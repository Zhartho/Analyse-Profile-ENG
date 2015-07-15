// ==UserScript==
// @name        Mush Analyse Profile ENG
// @namespace   Mush Analyse Profile
// @description Analysis of Profile
// @include     http://mush.com/u/profile/*
// @include     http://mush.com/me*
// @include     http://mush.twinoid.*/me*
// @include     http://mush.twinoid.*/u/profile/*
// @version     1.2
// ==/UserScript==

var $ = unsafeWindow.jQuery;

var scriptVersion = '1.1';

function addGlobalStyle(string){
        if(/microsoft/i.test(navigator.appName) && 
!/opera/i.test(navigator.userAgent)){
            document.createStyleSheet().cssText=string;
        }
        else {
            var ele=document.createElement('link');
            ele.rel='stylesheet';
            ele.type='text/css';
            ele.href='data:text/css;charset=utf-8,'+escape(string);
            document.getElementsByTagName('head')[0].appendChild(ele);
        }
}

addGlobalStyle('\
div.analyseProfile ul { float: left; background-color: #2E3B92; border-radius: 3px 3px 3px 3px;  border-style: solid; display: inline-block;  margin: 15px 4px; padding: 9px;  } \
div.analyseProfile ul li { font-weight: bold; text-align: center;   } \
');

function charaToClassChar(str)
{
	str = str.toLowerCase();
	str = str.replace(' ', '_');
	
	return str;
}

function Analyse_AddTable(n)
{	
	var infos = Analyse_Analyse(n);
	if(n == 0) { var txtN = 'All analyzed games'; } else { var txtN = n + ' analyzed games'; } 
	
	var tabHtml = '\
		<div id="AnalyseProfile_Result" class="bgtablesummar">\
			<div class="twinstyle"> \
				<h3> \
					<div class="cornerright"> \
						Script "Mush Analyse Profile v' + scriptVersion + ' - ' + txtN + ' - <a style="cursor: pointer;" id="AnalyseProfile_defineN">Edit this number</a>" \
					</div> \
				</h3>';
				
		//Infos globales
			//Nbr de parties
			tabHtml += '<div class="analyseProfile">';
			tabHtml += '<div style="float: left; display: inline-block; width: 30px;"> <span style="visibility: hidden;">42</span> </div><ul><li>Games Played : ' + infos['nbrGames'] + '</li> ';
				if(infos['nbrGamesBeta'] > 0)
				{
					tabHtml += '<li><img src="http://mush.vg/img/icons/ui/beta.png" /> Parties Beta supplémentaires : ' + infos['nbrGamesBeta'] + '</li>';
				}
				
			//Jours de jeu
			tabHtml += '<br /><li><img src="http://mush.vg/img/icons/ui/calendar.png" / > Total days : ' + infos['totalDay'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/calendar.png" /> Average day reached : <em>' + (infos['totalDay']/infos['nbrGames']).toFixed(2) + '</em></li>';
			
			//Triomphe
			tabHtml += '<br /><li><img src="http://mush.vg/img/icons/ui/triumph.png" / > Total Glory : ' + infos['totalTriumph'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/triumph.png" /> Average Glory : <em>' + (infos['totalTriumph']/infos['nbrGames']).toFixed(2) + '</em></li></ul>';
		
		//Recherches et Projets
			//Recherches
			tabHtml += '<ul><li><img src="http://mush.vg/img/icons/ui/microsc.png" /> Total Researches Completed : ' + infos['totalSearch'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/research_done.png" /> Average Researches per game: <em>' + (infos['totalSearch']/infos['nbrGames']).toFixed(2) + '</em></li>';

			//Projets
			tabHtml += '<br /><li><img src="http://mush.vg/img/icons/ui/conceptor.png" / > Total Projects Completed : ' + infos['totalProjects'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/projects_done.png" /> Average Projects per game : <em>' + (infos['totalProjects']/infos['nbrGames']).toFixed(2) + '</em></li></ul>';
		
		//Scan et explo
			//Recherches
			tabHtml += '<ul><li><img src="http://mush.vg/img/icons/ui/planet.png" /> Total Planet Scans : ' + infos['totalPlanetsScan'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/planet_complete.png" /> Average Planet Scans per game : <em>' + (infos['totalPlanetsScan']/infos['nbrGames']).toFixed(2) + '</em></li>';

			//Projets
			tabHtml += '<br /><li><img src="http://mush.vg/img/icons/ui/survival.png" / > Total Expos : ' + infos['totalExplo'] + '</li> ';
			tabHtml += '<li><img src="http://mush.vg/img/icons/ui/survival.png" /> Average Expo per game : <em>' + (infos['totalExplo']/infos['nbrGames']).toFixed(2) + '</em></li></ul>';
			
		
		tabHtml += '<div style="clear: both;"></div> \
		<div style="float: left; display: inline-block; width: 125px;"> <span style="visibility: hidden;">42</span> </div> \
		<table style="display: inline-block; float: left;" class="summar"> \
				<caption>Characters</caption> \
				<tbody> \
					<tr>\
						<th>Character</th> \
						<th>Games Played</th> \
					</tr> ';
					
			for(var c in infos['charaSorted'])
			{
				var l = infos['charaSorted'][c].length;
				for(var k =0; k < l; k++)
				{
					tabHtml += '\
					<tr> \
						<td><img class="char ' + charaToClassChar(infos['charaSorted'][c][k][1]) + '" src="/img/design/pixel.gif"> <span class="charname">' + infos['charaSorted'][c][k][1] + '</span></td> \
						<td>' + infos['charaSorted'][c][k][0] + ' (<em>' + (100*infos['charaSorted'][c][k][0]/infos['nbrGames']).toFixed(2) + '</em>%)</td> \
					</tr>';
				}
			} 
			tabHtml += '\
				</tbody> \
			</table> \
			<table style="display: inline-block; float: left;" class="summar"> \
				<caption>Morts</caption> \
				<tbody> \
					<tr>\
						<th>Cause of death</th> \
						<th>Number of deaths</th> \
					</tr> \
				';
			for(var d in infos['deathSorted'])
			{
				var l = infos['deathSorted'][d].length;
				for(var k =0; k < l; k++)
				{
					tabHtml += '\
					<tr> \
						<td>' + infos['deathSorted'][d][k][1] + '</td> \
						<td>' + infos['deathSorted'][d][k][0] + ' (<em>' + (100*infos['deathSorted'][d][k][0]/infos['nbrGames']).toFixed(2) + '</em>%)</td> \
					</tr>';
				}
			}
		tabHtml += '\
				</tbody> \
			</table> \
			<div style="clear: both;"></div> ';
		
	tabHtml += '\
			</div> \
		</div> \
	';
	
	/* tabHtml += '\
	<div class="bgtablesummar">\
		<div class="twinstyle"> \
			<h3> \
				<div class="cornerright"> \
					Script "Mush Analyse Profile v' + scriptVersion + '" \
				</div> \
			</h3>\
			<table class="summar"> \
				<caption>Infos joueurs et vaisseaux</caption> \
				<tbody> \
					<tr>\
						<th>Nombre de parties</th> \
						<th>Jours cumulés</th> \
						<th>Jour moyen</th> \
						<th>Triomphe cumulé</th> \
						<th>Triomphe moyen</th> \
						<th>Recherches cumulées</th> \
						<th>Recherches moyennes</th> \
						<th>Projets cumulés </th> \
						<th>Projets moyens</th> \
					</tr> \
					<tr>';
					if(infos['nbrGamesBeta'] > 0)
					{
						tabHtml += '<td>' + infos['nbrGames'] + ' <br /> + Beta : ' + infos['nbrGamesBeta'] + '</td>';
					}
					else
					{
						tabHtml += '<td>' + infos['nbrGames'] + '</td>';
					}
						tabHtml += '<td>' + infos['totalDay'] + '</td> \
						<td>' + (infos['totalDay']/infos['nbrGames']).toFixed(2) + '</td> \
						<td>' + infos['totalTriumph'] + '</td> \
						<td>' + (infos['totalTriumph']/infos['nbrGames']).toFixed(2) + '</td> \
						<td>' + infos['totalSearch'] + '</td> \
						<td>' + (infos['totalSearch']/infos['nbrGames']).toFixed(2) + '</td> \
						<td>' + infos['totalProjects'] + '</td> \
						<td>' + (infos['totalProjects']/infos['nbrGames']).toFixed(2) + '</td> \
					</tr> \
				</tbody> \
			</table> \
			<br /><br /> \
			<table class="summar"> \
				<caption>Infos joueurs et vaisseaux</caption> \
				<tbody> \
					<tr>\
						<th>Planètes scannées cumulées</th> \
						<th>Planètes scannées moyennes</th> \
						<th>Explorations cumulées</th> \
						<th>Explorations moyennes</th> \
					</tr> \
					<tr> \
						<td>' + infos['totalPlanetsScan'] + '</td> \
						<td>' + (infos['totalPlanetsScan']/infos['nbrGames']).toFixed(2) + '</td> \
						<td>' + infos['totalExplo'] + '</td> \
						<td>' + (infos['totalExplo']/infos['nbrGames']).toFixed(2) + '</td> \
					</tr> \
				</tbody> \
			</table> \
			<br /> \
			<table style="display: inline-block; float: left;" class="summar"> \
				<caption>Personnages</caption> \
				<tbody> \
					<tr>\
						<th>Personnage</th> \
						<th>Parties jouées</th> \
					</tr> \
				';
			for(var c in infos['charaSorted'])
			{
				var l = infos['charaSorted'][c].length;
				for(var k =0; k < l; k++)
				{
					//alert('L : ' + l + ' | K : ' + k + ' | NbrG : ' + infos['charaSorted'][c][k][0] + ' | Chara : ' + infos['charaSorted'][c][k][1]);
					tabHtml += '\
					<tr> \
						<td><img class="char ' + infos['charaSorted'][c][k][1].toLowerCase() + '" src="/img/design/pixel.gif"> <span class="charname">' + infos['charaSorted'][c][k][1] + '</span></td> \
						<td>' + infos['charaSorted'][c][k][0] + ' (<em>' + (100*infos['charaSorted'][c][k][0]/infos['nbrGames']).toFixed(2) + '</em>%)</td> \
					</tr>';
				}
			} 
			tabHtml += '\
				</tbody> \
			</table> \
			<table style="display: inline-block; float: left;" class="summar"> \
				<caption>Deaths</caption> \
				<tbody> \
					<tr>\
						<th>Cause de mort</th> \
						<th>Nombre de décès</th> \
					</tr> \
				';
			for(var d in infos['deathSorted'])
			{
				var l = infos['deathSorted'][d].length;
				for(var k =0; k < l; k++)
				{
					tabHtml += '\
					<tr> \
						<td>' + infos['deathSorted'][d][k][1] + '</td> \
						<td>' + infos['deathSorted'][d][k][0] + ' (<em>' + (100*infos['deathSorted'][d][k][0]/infos['nbrGames']).toFixed(2) + '</em>%)</td> \
					</tr>';
				}
			}
	tabHtml += '\
				</tbody> \
			</table> \
			<div style="clear: both;"></div> \
		</div> \
	</div> \
	'; */
	
	$('#profile > div.column2 > div.data > .bgtablesummar:first').after(tabHtml);
	
	$('#AnalyseProfile_defineN').click(function() {
		var n = prompt("Nombre de parties à analyser (0 = toutes) ?");
		Analyse_Init(n);
		return false;
	});
}

function Analyse_Analyse(n)
{
	var infos = new Array();
	infos['totalDay'] = 0;
	infos['totalSearch'] = 0;
	infos['totalProjects'] = 0;
	infos['totalPlanetsScan'] = 0;
	infos['totalExplo'] = 0;
	infos['totalTriumph'] = 0;
	infos['allDeaths'] = new Array();
	infos['allCharacters'] = new Array();
	
	infos['nbrGames'] = 0 ; //Nbr de parties
	infos['nbrGamesBeta'] = 0; //Parties en Beta
	
	$('#cdTrips > table.summar > tbody > tr.cdTripEntry').each(function(index,elem){
		//Character, Day, Explo, search, projets, scan, titres, triomphe, mort, vaisseau
		var death = $(this).children('td:eq(8)').text();
		
		if(n == 0 || infos['nbrGames'] < n)
		{
			if(death != 'Aucune infirmerie disponible')
			{
				infos['nbrGames']++;
				
				if(infos['allDeaths'][death] > 0)
				{
					infos['allDeaths'][death]++;
				}
				else
				{
					infos['allDeaths'][death] = 1;
				}
				
				var character = $(this).children('td:eq(0)').children('span.charname').text();
				
				if(infos['allCharacters'][character] > 0)
				{
					infos['allCharacters'][character]++;
				}
				else
				{
					infos['allCharacters'][character] = 1;
				}
				
				var d = $(this).children('td:eq(1)').text();
				infos['totalDay'] += parseInt(d);
				
				var e = $(this).children('td:eq(2)').text();
				infos['totalExplo'] += parseInt(e);
				
				var s = $(this).children('td:eq(3)').text();
				infos['totalSearch'] += parseInt(s);
				
				var p = $(this).children('td:eq(4)').text();
				infos['totalProjects'] += parseInt(p);
				
				var sc = $(this).children('td:eq(5)').text();
				infos['totalPlanetsScan'] += parseInt(sc);
				
				var t = $(this).children('td:eq(7)').text();
				infos['totalTriumph'] += parseInt(t);
			}
			else
			{
				infos['nbrGamesBeta']++;
			}
		}
			
	}); 
	
	//Trie morts/persos
	infos['charaSorted'] = new Array();
	for(var c in infos['allCharacters'])
	{
		infos['charaSorted'][infos['allCharacters'][c]] = new Array();
	}
	
	for(var c in infos['allCharacters'])
	{
		infos['charaSorted'][infos['allCharacters'][c]].push(new Array(infos['allCharacters'][c], c));
	}
	
	infos['charaSorted'].sort(function(a,b){return a - b});
	
	infos['deathSorted'] = new Array();
	for(var d in infos['allDeaths'])
	{
		infos['deathSorted'][infos['allDeaths'][d]] = new Array();
	}
	
	for(var d in infos['allDeaths'])
	{
		infos['deathSorted'][infos['allDeaths'][d]].push(new Array(infos['allDeaths'][d], d));
	}
	
	infos['deathSorted'].sort(function(a,b){return a - b});
	
	
	return infos;
}

function Analyse_Init(n)
{
	if(n < 0) { n = 0; }
	
	$('#AnalyseProfile_Result').remove();
	
	Analyse_AddTable(n);
	//alert('Test');
}

Analyse_Init(0);
