<script>
  import {Template} from 'svelte-native/components'
  import {login} from 'tns-core-modules/ui/dialogs'

  const colors = ['red', 'orange', 'yellow', 'green', 'blue', 'purple'];

  const myHtml = `
    <div>
      <span style="color: red">The quick brown fox</span>
      <span>jumps over</span>
      <span style="color: blue">the lazy dog</span>
    </div>
  `;
  //const myHtml = '<h1 style="color: red">Test</h1>';
  const temperatures = ['Cold', 'Warm', 'Hot'];

  let authenticated = false;
  let backgroundColor;
  let birthday = new Date(1961, 3, 16);
  let busy = false;
  let favoriteColorIndex = 0;
  let firstName = '';
  let progressPercent = 0;
  let likeRunning = false;
  let query = '';
  let quittingTime = new Date();
  let reason = '';
  let selectedTab;
  let stars = 3;
  let temperatureIndex = 1;

  quittingTime.setHours(17);
  quittingTime.setMinutes(0);
  quittingTime.setSeconds(0);

  $: backgroundColor = authenticated ? 'lightgreen' : 'pink';
  $: favoriteColor = colors[favoriteColorIndex];

  async function checkColor() {
    if (favoriteColor === 'yellow') {
      alert({
        title: 'Hey there!',
        message: 'That is my favorite color too!',
        okButtonText: 'Cool'
      });
    } else if (favoriteColor === 'green') {
      const confirmed = await confirm({
        title: 'Confirm Color',
        message: 'Are you sure you like that color?',
        okButtonText: 'Yes',
        cancelButtonText: 'No'
      });

      if (!confirmed) favoriteColorIndex = 0;
    }
  }

  $: checkColor(favoriteColor);

  function formatDate(date) {
    if (!date) return '';
    let month = date.toLocaleDateString('default', {month: 'long'});
    // What toLocaleDateString returns differs between JS engines.
    const index = month.indexOf(' ');
    if (index) month = month.substring(0, index);
    return `${month} ${date.getDate()}, ${date.getFullYear()}`;
  }

  function formatTime(date) {
    if (!date) return '';

    let hours = date.getHours();
    const amPm = hours < 12 ? 'AM' : 'PM';
    if (hours >= 12) hours -= 12;

    let minutes = date.getMinutes();
    if (minutes < 10) minutes = '0' + minutes;

    return `${hours}:${minutes} ${amPm}`;
  }

  async function getFirstName() {
    const NONE = 'No Thanks';
    const res = await prompt({
      title: 'First Name',
      message: 'Please tell me your first name.',
      okButtonText: 'Here it is',
      cancelButtonText: 'I will not share that'
    });
    if (res.result) firstName = res.text;
  }

  function onFavoriteColor(event) {
    favoriteColorIndex = event.value; // weird that this is an index
  }

  function onSearchSubmit() {
    busy = true;
    setTimeout(async () => { // to demonstrate activityIndicator component
      // The event object passed to this does not contain the search text
      // or any interesting properties.
      const q = query.toLowerCase();
      const matches = colors.filter(color => color.includes(q));
      busy = false;
      await alert({
        title: 'Color Matches',
        message: matches.length ? matches.join(', ') : 'no matches',
        okButtonText: 'Close'
      });
      query = ''; // reset
    }, 1000);
  }

  function onTapColor(event) {
    favoriteColorIndex = event.index;
  }

  function onTapDelete() {
    console.log('App.svelte onTapDelete: entered');
  }

  function onTapShare() {
    console.log('App.svelte onTapShare: entered');
  }

  async function pickColor() {
    const NONE = 'No Thanks';
    const choice = await action('Pick a color', NONE, colors);
    //if (choice !== NONE) favoriteColor = choice;
    if (choice !== NONE) {
      favoriteColorIndex = colors.findIndex(c => c === choice);
    }
  }

  async function promptForLogin() {
    const res = await login({
      title: 'Please Sign In',
      message: 'This will unlock more features.',
      userNameHint: 'username',
      passwordHint: 'password',
      okButtonText: 'OK',
      cancelButtonText: 'Cancel',
    });
    /*
    const res = await login(
      'This will unlock more features.',
      'username',
      'password'
    );
    */
    if (res.result) {
      // Authenticate the user here.
      authenticated = res.userName === 'foo' && res.password === 'bar';
      if (!authenticated) {
        alert({
          title: 'Login Failed',
          message: 'Your username or password was incorrect.',
          okButtonText: 'Bummer'
        });
      }
    }
  }

  function starChange(event) {
    console.log('App.svelte starChange: event.value =', event.value);
    stars = Math.round(event.value);
    console.log('App.svelte starChange: stars =', stars);
  }

  function startProgress() {
    progressPercent = 0;
    const token = setInterval(() => {
      progressPercent++;
      if (progressPercent === 100) clearInterval(token);
    }, 10);
  }
</script>

<page>
  <!-- TODO: Still need to demo Tabs, TabStrip, TabStripItem, and TabContentItem.
       BottomNavigation has the same children as Tabs.

       Modify this to display different categories of components
       on different pages.
  -->

  <actionBar title="Svelte Native Demo">
    <!-- button with upload icon -->
    <actionItem on:tap="{onTapShare}"
      ios.systemIcon="9" ios.position="left"
      android.systemIcon="ic_menu_share" android.position="actionBar" />
    <!-- button with trashcan icon -->
    <actionItem on:tap="{onTapDelete}"
      ios.systemIcon="16" ios.position="right"
      text="delete" android.position="popup" />
  </actionBar>

  <tabs bind:selectedIndex={selectedTab}>
    <tabStrip>
      <tabStripItem>
        <label text="Display" />
        <image src="font://&#xf015;" class="fas t-36" />
      </tabStripItem>
      <tabStripItem>
        <label text="Input" />
        <image src="font://&#xf007;" class="fas t-36" />
      </tabStripItem>
      <tabStripItem>
        <label text="Other" />
        <image src="font://&#xf00e;" class="fas t-36" />
      </tabStripItem>
    </tabStrip>

    <!-- Display content -->
    <tabContentItem>
      <scrollView>
        <stackLayout
          backgroundColor={backgroundColor}
          class="p-20"
        >
          <label class="title" text="label" />
          <label class="plain" text="some text" />

          <label class="title" text="label with formattedString" />
          <label class="panagram" textWrap="true">
            <formattedString>
              <span class="fox">The quick brown fox</span>
              <span text=" jumps over " />
              <span class="dog">the lazy dog</span>
              <span text="." />
            </formattedString>
          </label>

          <label class="title" text="image" />
          <image src="~/svelte-native-logos.png" stretch="aspectFit" />

          <!-- The NativeScript docs say
              "The HtmlView component has limited styling capabilities.
              For more complex scenarios use the WebView component." -->
          <!--htmlView html={myHtml} /-->

          <!-- webView components may need to be given a height in CSS.
              See https://github.com/halfnelson/svelte-native/issues/132. -->
          <webView src={myHtml} />

          <!--TODO: It appears that you cannot pass a string of HTML
              as the value of the src attribute. -->
          <!--webView src="<div><h1>I am a webView.</h1></div>" /-->

          <!--TODO: Try to get this to work. -->
          <!--webView src="~/demo.html" /-->

          <label class="title" text="progress" />
          <button on:tap={startProgress}>Start Progress</button>
          <progress class="progress" maxValue={100} value="{progressPercent}" />
        </stackLayout>
      </scrollView>
    </tabContentItem>

    <!-- Input content -->
    <tabContentItem>
      <scrollView>
        <stackLayout
          backgroundColor={backgroundColor}
          class="p-20"
        >
          <!-- like HTML <input type="text"> -->
          <label class="title" text="textField" />
          <wrapLayout>
            <label text="First Name" />
            <textField class="first-name" hint="first name" bind:text={firstName} />
            <label class="plain" text="Your first name is {firstName}." />
          </wrapLayout>

          <!-- like HTML <textarea> -->
          <label class="title" text="textView" />
          <wrapLayout>
            <label text="What would you say you do here?" />
            <textView class="reason" hint="reason for being" bind:text={reason} />
            <label class="plain" text="Your reason for being is {reason}." textWrap="true" />
          </wrapLayout>

          <!-- like HTML <input type="checkbox"> -->
          <label class="title" text="switch" />
          <wrapLayout>
            <label text="Like Running?" />
            <switch bind:checked={likeRunning} />
            <label
              class="plain"
              text="You{likeRunning ? '' : ' do not'} like running."
            />
          </wrapLayout>

          <!-- like HTML <input type="radio"> -->
          <label class="title" text="segmentedBar" />
          <segmentedBar
            bind:selectedIndex={temperatureIndex}
            selectedBackgroundColor="yellow"
          >
            <segmentedBarItem title="Cold" />
            <segmentedBarItem title="Warm" />
            <segmentedBarItem title="Hot" />
          </segmentedBar>

          <!-- The API docs show passing an array of items
              using the "items" prop, but that does not work.
              The children must be segmentedBarItem components. -->
          <segmentedBar
            bind:selectedIndex={temperatureIndex}
            selectedBackgroundColor="yellow"
          >
            {#each temperatures as temp}
              <segmentedBarItem title={temp} />
            {/each}
          </segmentedBar>
          <label
            class="plain"
            text="You are feeling {temperatures[temperatureIndex]}."
          />

          <!-- like HTML <input type="date"> -->
          <label class="title" text="datePicker" />
          <wrapLayout>
            <label text="Birthday" />
            <datePicker bind:date={birthday} />
            <label class="plain" text="You selected {formatDate(birthday)}." />
          </wrapLayout>

          <!-- like HTML <input type="time"> -->
          <label class="title" text="timePicker" />
          <wrapLayout>
            <label text="Quitting Time" />
            <timePicker bind:time={quittingTime} />
            <label class="plain" text="You will quit at {formatTime(quittingTime)}." />
          </wrapLayout>

          <!-- like HTML <ul>, <ol>, or <select> -->
          <label class="title" text="listView" />
          <wrapLayout>
            <listView items={colors} on:itemTap={onTapColor}>
              <Template let:item={color}>
                <!--TODO: Why doesn't class:selected get reevaluated when favoriteColor changes? -->
                <label
                  class="list"
                  class:selected={color === favoriteColor}
                  text="One of the colors is {color}."
                />
              </Template>
            </listView>
          </wrapLayout>
          <label class="plain" text="You selected {favoriteColor}." />

          <!-- like HTML <select> -->
          <label class="title" text="listPicker" />
          <wrapLayout>
            <label text="Favorite Color" />
            <!--TODO: See https://github.com/halfnelson/svelte-native/issues/129 -->
            <!--listPicker items={colors} bind:selectedIndex={favoriteColorIndex} 
              on:selectedIndexChange={onFavoriteColor} /-->
            <listPicker
              items={colors}
              selectedIndex={favoriteColorIndex}
              on:selectedIndexChange={onFavoriteColor}
            />
            <label class="plain" text="You selected {favoriteColor}." />
          </wrapLayout>

          <!-- like HTML <input type="range"> -->
          <label class="title" text="slider" />
          <wrapLayout>
            <label text="Stars" />
            <!-- The slider position doesn't change when
                JavaScript code changes the value of stars.
                See https://github.com/halfnelson/svelte-native/issues/128. -->
            <slider
              minValue={1}
              maxValue={5}
              value={stars}
              on:valueChange={starChange}
            />
            <label class="plain" text="You rated it {stars} stars" />
          </wrapLayout>

        </stackLayout>
      </scrollView>
    </tabContentItem>

    <!-- Other content -->
    <tabContentItem>
      <scrollView>
        <stackLayout
          backgroundColor={backgroundColor}
          class="p-20"
        >
          <label class="title" text="login" />
          <wrapLayout>
            {#if authenticated}
              <button on:tap={() => authenticated = false}>Logout</button>
            {:else}
              <button on:tap={promptForLogin}>Login ...</button>
            {/if}
          </wrapLayout>

          <label class="title" text="prompt" />
          <button on:tap={getFirstName}>Prompt for First Name</button>
          <label class="plain" text="Your first name is {firstName}." />

          <label class="title" text="action" />
          <button on:tap={pickColor}>Pick a Color</button>
          <label class="plain" text="You selected {favoriteColor}." />

          <label class="title" text="searchBar" />
          <!-- Using gridLayout to position activityIndicator over searchBar. -->
          <gridLayout rows="*">
            <searchBar
              hint="Enter part of a color name."
              bind:text={query}
              on:submit={onSearchSubmit}
              row="0"
            />
            <!-- The activityIndication height and width attributes
                control the allocated space.
                But the size of the spinner cannot be changed,
                only the color. -->
            <activityIndicator busy={busy} row="0"/>
          </gridLayout>
        </stackLayout>
      </scrollView>
    </tabContentItem>
  </tabs>
</page>

<style>
  activityIndicator {
    color: blue;
  }

  button {
    background-color: lightgray;
    border-color: darkgray;
    border-width: 3;
    border-radius: 10;
    font-weight: bold;
    horizontal-align: center; /* to size according to content */
    padding: 10;
  }

  .fox {
    color: red;
    font-weight: bold;
  }

  .dog {
    color: blue;
    font-style: italic;
  }

  .first-name {
    width: 100%;
  }

  image {
    border-color: green;
    border-width: 1;
  }

  label {
    color: blue;
    font-size: 18;
    font-weight: bold;
  }

  .list {
    border-color: blue;
    border-width: 1;
    margin: 0;
  }

  .panagram {
    background-color: linen;
    color: black;
    font-size: 26;
    font-weight: normal;
    margin-bottom: 20;
    padding: 20;
  }

  .plain {
    color: black;
    font-size: 12;
    font-weight: normal;
  }

  progress {
    color: red;
    margin-bottom: 10;
    scale-y: 5; /* changes height */
  }

  .reason {
    width: 100%;
  }

  searchBar {
    margin-bottom: 10;
  } 

  segmentedBar {
    color: red;
    margin-bottom: 10;
    margin-top: 10;
  }

  .selected {
    background-color: lightgreen;
  }

  .title {
    border-top-color: purple;
    border-top-width: 5px;
    color: purple;
    font-size: 20;
    font-weight: bold;
    font-style: italic;
    margin-top: 10;
    padding-top: 10;
  }

  webView {
    border-color: red;
    border-width: 1;
    /*height: 50; /*TODO: Is this required? */
  }
</style>
