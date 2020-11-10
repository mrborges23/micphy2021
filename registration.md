## Registration

The registration is free but compulsory. Registration deadline: 9th February 2021.


<iframe src="https://docs.google.com/forms/d/e/1FAIpQLScDync9SFiZ8QTe_HtMi8P47mYH2len4Xcepf-unQVGwt0aPA/viewform?embedded=true" width="640" height="1757" frameborder="0" marginheight="0" marginwidth="0">Carregando…</iframe>


<form name="submit-to-google-sheet">
  <input name="name" type="text" placeholder="Email" required>
  <input name="email" type="email" placeholder="Email" required>
  <input name="affiliation" type="text" placeholder="Email" required>
  <input name="question1" type="text" placeholder="Email" required>
  <input name="abstract" type="text" placeholder="Email" required>
  <input name="question2" type="text" placeholder="Email" required>
  <input name="letter" type="text" placeholder="Email" required>
  <button type="submit">Send</button>
</form>


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