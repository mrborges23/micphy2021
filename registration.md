## Registration

The registration is free but compulsory. Registration deadline: 9th February 2021.


<iframe src="https://docs.google.com/forms/d/e/1FAIpQLScDync9SFiZ8QTe_HtMi8P47mYH2len4Xcepf-unQVGwt0aPA/viewform?embedded=true" width="640" height="1757" frameborder="0" marginheight="0" marginwidth="0">Carregando…</iframe>


<form id="test-form">
  
  <div>
    <label>Field 1</label>
    <input type="text" name="name" placeholder="Field 1"/>
  </div>

  <div>
    <label>Field 2</label>
    <input type="text" name="email" placeholder="Field 2"/>
  </div>
  
  <div>
    <label>Field 3</label>
    <input type="text" name="affiliation" placeholder="Field 3"/>
  </div>
  
  <div>
    <label>Field 4</label>
    <input type="text" name="contribution" placeholder="Field 4"/>
  </div>

  <div>
    <label>Field 5</label>
    <input type="text" name="abstract" placeholder="Field 4"/>
  </div>

  <div>
    <label>Field 6</label>
    <input type="text" name="workshop" placeholder="Field 4"/>
  </div>

  <div>
    <label>Field 7</label>
    <input type="text" name="letter" placeholder="Field 4"/>
  </div>

  <div>
    <button type="submit" id="submit-form">Submit</button>
  </div>

</form>


var $form = $('form#test-form'),
    url = 'https://script.google.com/macros/s/AKfycby9aKGewUqCF_RE6JInpvEsG-YvUR7-hl0UwaDDo40RelEGs9oe/exec'

$('#submit-form').on('click', function(e) {
  e.preventDefault();
  var jqxhr = $.ajax({
    url: url,
    method: "GET",
    dataType: "json",
    data: $form.serializeObject()
  }).success(
    // do something
  );
})