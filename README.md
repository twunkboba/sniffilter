# sniffilter

Proof of concept filter for a certain website

## Installation and Usage

This is accomplished via browser bookmarklet (sorry mobile users)

1. In a new browser window, go to https://caiorss.github.io/bookmarklet-maker/
2. Copy the code below and paste it in the "Code" textarea
3. Click [Generate Bookmarklet]
4. Underneath the [Generate Bookmarklet] button, you will see "Bookmarklet: [bookmarklet]" (blue button). Click and drag the blue button to your browser's bookmark bar
   - Note - most browsers only show the bookmark bar on a new window/tab. You will need to change your browser setting to always show the bookmark bar
6. Go to that certain website and click the bookmarklet. You will be prompted a series of questions to filter the profile bubbles on the map.

```
var ask=true;var filter={minAge:null,maxAge:null,maxWeight:null,requireAge:null,requireWeight:null};var cachedFilter=null;try{cachedFilter=window.localStorage.getItem('zfilter')}catch(e){}if(cachedFilter){if(window.confirm('Apply saved filter (OK) or create new filter (Cancel)?\n\n'+cachedFilter)){filter=JSON.parse(cachedFilter);ask=false}}if(ask){filter.minAge=window.prompt("Min age - press cancel or enter blank if you don't have a min age requirement");filter.maxAge=window.prompt("Max age - press cancel or enter blank if you don't have a max age requirement");filter.maxWeight=window.prompt("Max weight - press cancel or enter blank if you don't have a max weight requirement");filter.requireAge=window.confirm("Require age? OK = yes, Cancel = no");filter.requireWeight=window.confirm("Require weight? OK = yes, Cancel = no");try{window.localStorage.setItem('zfilter',JSON.stringify(filter))}catch(e){}}function parse(str){var ar=str.split(', ');var age=null;var heightFt=null;var heightIn=null;var weight=null;var position=null;ar.forEach(function(s){var ageMatch=s.match(/^\d+$/);var heightMatch=s.match(/^\d+'\d+"$/);var weightMatch=s.match(/^\d+lb/);var positionMatch=s.match(/top$|bottom$|^vers|^side/);if(ageMatch){age=parseInt(ageMatch[0],10)}if(heightMatch){var tmp=s.split("'");heightFt=parseInt(tmp[0],10);heightIn=parseInt(tmp[1],10)}if(weightMatch){weight=parseInt(weightMatch[0],10)}if(positionMatch){position=s}});return{age,heightFt,heightIn,weight,position}};function keep(str){var attrs=parse(str);if(filter.requireAge&&!attrs.age){return false}if(filter.requireWeight&&!attrs.weight){return false}if(filter.minAge&&attrs.age&&attrs.age<filter.minAge){return false}if(filter.maxAge&&attrs.age&&attrs.age>filter.maxAge){return false}if(filter.maxWeight&&attrs.weight&&attrs.weight>filter.maxWeight){return false}return true};document.querySelectorAll('.mgl-marker').forEach(function(el){el.style.display=''});document.querySelectorAll('.headline-preview .title-tag').forEach(function(el){if(!keep(el.innerText)){el.closest('.mgl-marker').style.display='none'}});
```

## Filters:
- min / max age
- max weight
- hide profiles that don't have an age
- hide profiles that don't have a weight

## Future:
- handle metric system
- position (top, bottom, vers, side, etc)
- 🍆 size and cut
- body type (fit, slim, average, stocky, etc)

## Limitations:
- profiles with absolutely no profile details (e.g. "Anonymous User", "Registerd Profile") are not filtered
- filter currently does not apply to "new bubbles" that appear on the map
  - temporary fix - re-run the filter bookmarklet
