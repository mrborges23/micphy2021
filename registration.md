## Registration

The registration is free but compulsory. Registration deadline: 9th February 2021.

Please, make sure you follow the guidelines to abstract and motivation letter submition; these can be found at the bootom of this page.

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
  <input type="radio" name="question1" value="1"> No <br>
  <input type="radio" name="question1" value="2"> Yes, oral presentation only <br>
  <input type="radio" name="question1" value="3"> Yes, oral or poster presentation <br>
  <input type="radio" name="question1" value="4"> Yes, poster presentation <br><br>

  Abstract:<br>
  <textarea rows="4" cols="50" name="abstract"></textarea>
  <br><br>

  Do you want to attend the workshop?: <br>
  <input type="radio" name="question2" value="1"> No <br>
  <input type="radio" name="question2" value="2"> Yes <br><br>
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
  document.formu1.reset()
</script>


**Abstract**
* Title in the first line 
* Authors list in the second line with affiliations in square brackets 
* Affiliation list in the third line. Identify each affilition using square brackets and separate affiliations by commas.
* Abstract text starting in the forth line
* Please do not include any pictures or references. 
* Limit: 2000 characters.

<br>

**Motivation letter to attend the workshop**
* Explain the reasons why you want to participate in the workshop. 
* Preference will be given to participants that are currently performing phylogenomic analysis in real data. 
* Limit: 2000 characters.
