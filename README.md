# Weather-App
<html> 
 <head> 
  <style>
     
::root {
  --bg_main: white;
  --text_light: red;
  --text_med: green;
  --text_dark: yellow;
 }
 
button,
input {
  border: black;       
  background: none;
  outline: none;.       //for button//
  color: inherit;
  cursor: pointer;
}

.top-banner {
  color: var(--text_color);.  //top banner is for heading//
}

.heading {
  font-weight: bold;
  font-size: 6rem;.     //top banner heading size//
  letter-spacing: 0.08em;
  padding: 0 0 10px 0;
}

.top-banner form input {
  font-size: 20px;
  height: 10px;
  padding: 15px 15px 15px;
  border-bottom: 1px solid;.      //for search//
}

.top-banner form button {
  font-size: 20px;
  font-weight: bold;
  letter-spacing: 2px;
  padding: 10px 10px;.    //for search button//
  margin-left: 50px;
}

.ajax-section {
  margin: 70px 0 20px;
}

 .ajax-section .cities {
  display: grid;
  grid-gap: 32px 20px;
  grid-template-columns: repeat(4, 1fr);
}

.ajax-section .city {
  position: relative;
  padding: 40px;
  border-radius: 20px;
  background: var(--text_light);
  color: var(--text_med);.       //for city//
}

.ajax-section .city::after {
  content: '';
  width: 1000%;
  height: 30px;
  position: absolute;
  bottom: 12px;
  left: 5%;
  z-index: -1;
  opacity: 0.3;
  border-radius: 20px;
  background: var(--text_light);
}
.ajax-section figcaption {
  margin-top: 10px;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.ajax-section .city-temp {
  font-size: 5rem;
  font-weight: bold;
  margin-top: 10px;
  color: var(--text_dark);
}

.ajax-section .city sup {
  font-size: 0.5em;
}
.ajax-section .city-name sup {
  padding: 0.2em 0.6em;
  border-radius: 30px;
  color: var(--text_light);
}

.ajax-section .city-icon {
  margin-top: 10px;
  width: 100px;
  height: 100px;
}

@media screen and (max-width: 1000px) {
  body {
    padding: 30px;
  }
  
  .ajax-section .cities {
    grid-template-columns: repeat(3, 1fr);
  }
}
@media screen and (max-width: 4000px) {
  .heading,
  .ajax-section .city-temp {
    font-size: 3rem;
  }
  
  .ajax-section {
    margin-top: 20px;
  }
  
  .top-banner form {
    flex-direction: column;
    align-items: flex-start;
  }
  
  .top-banner form input,
  .top-banner form button {
    width: 100%;
  }

  .top-banner form button {
    margin: 20px 0 0 0;
  }
  
  .top-banner form .msg {
    position: static;
    max-width: none;
    min-height: 0;
    margin-top: 10px;
  }

  .ajax-section .cities {
    grid-template-columns: repeat(2, 1fr);
  }
}
@media screen and (max-width: 500px) {
  body {
    padding: 15px;
  }
  
  .ajax-section .cities {
    grid-template-columns: repeat(1, 1fr);
  }
}
api {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  padding: 10px;
}

.api a {
  text-decoration: underline;
}

.api a:hover {
  text-decoration: none;
}

   </style> 
 </head> 
 <body> 
  <div class="api"> 
   <div class="container"> 
   </div> 
  </div> 
  <section class="top-banner"> 
   <div class="container"> 
    <h1 class="heading">SIMPLE WEATHER APP</h1> 
    <form> 
     <input type="text" placeholder="Search for a city" autofocus> <button type="submit">SUBMIT</button> <span class="msg"></span> 
    </form> 
   </div> 
  </section> 
  <section class="ajax-section"> 
   <div class="container"> 
    <ul class="cities"></ul> 
   </div> 
  </section> 
  <script>
 //SEARCH BY USING A CITY NAME (e.g. athens) OR A COMMA-SEPARATED CITY NAME ALONG WITH THE COUNTRY CODE (e.g. athens,gr)
const form = document.querySelector(".top-banner form");
const input = document.querySelector(".top-banner input");
const msg = document.querySelector(".top-banner .msg");
const list = document.querySelector(".ajax-section .cities");
/*SUBSCRIBE HERE FOR API KEY: https://home.openweathermap.org/users/sign_up*/
const apiKey = "4d8fb5b93d4af21d66a2948710284366";
form.addEventListener("submit", e => {
  e.preventDefault();
  let inputVal = input.value;

  //check if there's already a city
  const listItems = list.querySelectorAll(".ajax-section .city");
  const listItemsArray = Array.from(listItems);

  if (listItemsArray.length > 0) {
    const filteredArray = listItemsArray.filter(el => {
      let content = "";
      //athens,gr
if (inputVal.includes(",")) {
        //athens,grrrrrr->invalid country code, so we keep only the first part of inputVal
        if (inputVal.split(",")[1].length > 2) {
          inputVal = inputVal.split(",")[0];
          content = el
            .querySelector(".city-name span")
            .textContent.toLowerCase();
        } else {
          content = el.querySelector(".city-name").dataset.name.toLowerCase();
        }
      } else {
        //athens
        content = el.querySelector(".city-name span").textContent.toLowerCase();
      }
return content == inputVal.toLowerCase();
    });

    if (filteredArray.length > 0) {
      msg.textContent = `You already know the weather for ${
        filteredArray[0].querySelector(".city-name span").textContent
      } ...otherwise be more specific by providing the country code as well `;
      form.reset();
      input.focus();
      return;
    }
  }
   //ajax here
  const url = `https://api.openweathermap.org/data/2.5/weather?q=${inputVal}&appid=${apiKey}&units=metric`;

  fetch(url)
    .then(response => response.json())
    .then(data => {
      const { main, name, sys, weather } = data;
      const icon = `https://s3-us-west-2.amazonaws.com/s.cdpn.io/162656/${
        weather[0]["icon"]
      }.svg`;
const li = document.createElement("li");
      li.classList.add("city");
      const markup = `
      
        <h2 class="city-name" data-name="${name},${sys.country}">
          <span>${name}</span>
          <sup>${sys.country}</sup>
        </h2>
        <div class="city-temp">${Math.round(main.temp)}<sup>°C</sup></div>
        <figure>
          <img class="city-icon" src="${icon}" alt="${
        weather[0]["description"]
      }">
          <figcaption>${weather[0]["description"]}</figcaption>
        </figure>

  `;
      li.innerHTML = markup;
      list.appendChild(li);
    })
    .catch(() => {
      msg.textContent = "Please search for  valid city";
    });

  msg.textContent = "";
  form.reset();
  input.focus();
});
</script> 
 </body>
</html>
