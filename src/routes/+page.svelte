
<script>
  import { onMount } from 'svelte';

  let searchInput;
  let videoContainer;

  onMount(() => {
    // Add click event listener to the button
    const searchButton = document.getElementById('searchButton');
    searchButton.addEventListener('click', handleSearch);

    // Cleanup the event listener when the component is destroyed
    return () => {
      searchButton.removeEventListener('click', handleSearch);
    };
  });

  function handleSearch() {
    // Get the search term from the input field
    const searchTerm = searchInput.value;

    // Call a function to search within TMDb with the provided term
    searchTMDb(searchTerm);
  }

  // Function to search within TMDb
  function searchTMDb(searchTerm) {
    // You can use the TMDb API here with your provided API key
    // Construct the URL for searching movies, for example:
    const tmdbApiUrl = 'https://api.themoviedb.org/3/search/movie';
    const apiKey = 'ecd2ddd34ecac72bb110a861ff9785e7'; // Replace with your actual TMDb API key

    // Make an API request using Fetch or another AJAX library
    fetch(`${tmdbApiUrl}?api_key=${apiKey}&query=${searchTerm}`)
      .then(response => response.json())
      .then(data => {
        // Process the data, for example, get the ID of the first result
        const firstResult = data.results[0];
        if (firstResult) {
          const movieId = firstResult.id;
          console.log('Found movie ID:', movieId);

          // Embed the video using an iframe
          embedVideo(movieId);
        } else {
          console.log('No results found');
        }
      })
      .catch(error => {
        console.error('Error fetching data from TMDb:', error);
      });
  }

  // Function to embed the video using the movie ID
  function embedVideo(movieId) {
    // Construct the URL for embedding the video
    const videoUrl = `https://vidsrc.to/embed/movie/${movieId}`;

    // Create an iframe element
    const iframe = document.createElement('iframe');
    iframe.setAttribute('src', videoUrl);
    iframe.setAttribute('width', '100%');
    iframe.setAttribute('height', '400'); // Adjust the height as needed
    iframe.setAttribute('frameborder', '0');
    iframe.setAttribute('allowfullscreen', 'true');

    // Clear the previous video, if any
    videoContainer.innerHTML = '';

    // Append the iframe to the video container
    videoContainer.appendChild(iframe);
  }
</script>

<div class="relative bg-white w-full h-[46.25rem] overflow-hidden flex flex-col items-center justify-start pt-[14.13rem] pb-[24.63rem] pr-[1.25rem] pl-[1.31rem] box-border gap-[2.56rem] tracking-[normal] mq700:gap-[1.25rem]">
  <img class="absolute my-0 mx-[!important] h-full w-full top-[0rem] right-[0rem] bottom-[0rem] left-[0rem] max-w-full overflow-hidden max-h-full object-cover" alt="" src="/Firefox_wallpaper.png" />

  <input bind:this={searchInput} class="[outline:none] bg-silver relative box-border w-[41.94rem] h-[2.31rem] overflow-hidden shrink-0 max-w-full border-[1px] border-solid border-black" type="text" />

  <button id="searchButton" class="cursor-pointer p-0 bg-midnightblue rounded-[10px] w-[9.69rem] flex flex-row items-center justify-center hover:bg-darkslateblue">
    <div id="searchText" class="flex-1 relative text-[1.25rem] font-inter text-white text-center flex items-center justify-center h-[2.63rem] mq450:text-[1rem]"> Search </div>
  </button>

  <!-- Container for the embedded video -->
  <div bind:this={videoContainer}></div>
</div>

