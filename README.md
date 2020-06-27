# Collections-search-plugins
Дополнение для модуля [коллекций](https://github.com/TeraMoune/Collections-DLE), позволяет производить поиск подборок, а так же новостей в подборке.

Разметка шаблона `collections_fsearch.tpl` ответа результатов поиска стандартная и включает все теги.
Есть только два главных и основных тега в нутри которых работают другие, название которых говорит само за себя.

Тег **[news-search]**, не обязателен. В случае его отсуствия просто не будет искать новости если пользователь находится в подборке.
 - **[news-search] ... [/news-search]** - Обозначает разметку ответов для новостей.
 - **[collections-search] ... [/collections-search]** - Обозначает разметку ответов для подборок.
    - **{full_link}**
    - **{cover}**
    - **{name}**
    - **{description}**

В файле engine.css скорей всего будут стандартные стили для подсказки быстрого поиска, начинаются они с `#searchsuggestions`, скопируйте их и продублируйте каждый ID обозвав его иначе, например `#searchsuggestions_c`.

**JavaScript** функция
```JS
function FastSearchCollections() {
	$('#story_c').attr('autocomplete', 'off');
	$('#story_c').blur(function(){
		 	$('#searchsuggestions_c').fadeOut();
	});

	$('#story_c').keyup(function() {
		var inputString = $(this).val();

		if(inputString.length == 0) {
			$('#searchsuggestions_c').fadeOut();
			$('#story_c').next().fadeOut();
		} else {
			$('#story_c').next().fadeIn();
			if (dle_search_c_value != inputString && inputString.length > 2) {
				clearInterval(dle_search_c_delay);
				dle_search_c_delay = setInterval(function() { dle_do_search_collections(inputString); }, 600);
			}

		}
	
	});
};

function dle_do_search_collections( inputString ) {
	
	var req_href = location.href;
	var result = req_href.match(/&id=(\d+)/);
	
	clearInterval(dle_search_c_delay);

	$('#searchsuggestions_c').remove();

	$("body").append("<div id='searchsuggestions_c' style='display:none'></div>");

	$.post(dle_root + "engine/ajax/controller.php?mod=search_collections", {query: ""+inputString+"", collections: (result ? result[1] : ''), user_hash: dle_login_hash}, function(data) {
			$('#searchsuggestions_c').html(data).fadeIn().css({'position' : 'absolute', top:0, left:0, width: $('.quicksearch_c').width()+'px'}).position({
				my: "left top",
				at: "left bottom",
				of: "#story_c",
				collision: "fit flip"
			});
		});

	dle_search_c_value = inputString;

};
```
**Разметка поля для ввода поисковых запросов**
```HTML
	<div class="quicksearch_c">
			<input id="story_c" class="tokenfield form-control" placeholder="Поиск..." name="story" value="" type="search">
	</div>
```
