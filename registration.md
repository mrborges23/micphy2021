## Registration

The registration is free but compulsory. Registration deadline: 9th February 2021.


<iframe src="https://docs.google.com/forms/d/e/1FAIpQLScDync9SFiZ8QTe_HtMi8P47mYH2len4Xcepf-unQVGwt0aPA/viewform?embedded=true" width="640" height="1757" frameborder="0" marginheight="0" marginwidth="0">Carregando…</iframe>



<form name="submit-to-google-sheet">
  <br>
  Name:<br>
  <input type="text" name="name" value="">
  <br>
  Email:<br>
  <input type="text" name="email" value="">
  <br>
  Affiliation:<br>
  <input type="text" name="affiliation" value="">
  <br><br>
  Do you want to attend the workshop?: <br>
  <input type="radio" name="question1" value="0"> No <br>
  <input type="radio" name="question1" value="1"> Yes, oral presentation only <br>
  <input type="radio" name="question1" value="2"> Yes, oral or poster presentation <br>
  <input type="radio" name="question1" value="3"> Yes, poster presentation <br><br>

  Abstract:<br>
  <textarea rows="4" cols="50" name="abstract"></textarea>
  <br><br>

  Do you want to attend the workshop?: <br>
  <input type="radio" name="workshop" value="0"> No <br>
  <input type="radio" name="workshop" value="1"> Yes <br><br>
  Motivation letter:<br>
  <textarea rows="4" cols="50" name="letter"></textarea>

  <br><br>
  <button type="submit">Send</button>
</form> 
<br>






<script>
  const scriptURL = 'https://script.google.com/macros/s/AKfycbyJClSf277gOQYgH2cxLkYSe6uXDj_1AE-Zl-0qc5YY4KEpOjJo/exec'
  const form = document.forms['submit-to-google-sheet']

  form.addEventListener('submit', e => {
    e.preventDefault()
    fetch(scriptURL, { method: 'POST', body: new FormData(form)})
      .then(response => console.log('Success!', response))
      .catch(error => console.error('Error!', error.message))
  })
</script>