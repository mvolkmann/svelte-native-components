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
  let birthday = new Date(1961, 3, 16);
  let busy = false;
  let favoriteColorIndex = 0;
  let favoriteColor = colors[favoriteColorIndex];
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

  //$: favoriteColor = colors[favoriteColorIndex];

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
    const index = event.value; // weird that this is an index
    favoriteColor = colors[index];
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
    const color = colors[event.index];
    console.log(`You tapped the color ${color}.`);
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
    if (choice !== NONE) favoriteColor = choice;
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
  <!-- TODO: Still need to demo TabStrip, TabStripItem, TabContentItem,
       BottomNavigation, and Tabs.
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

  <scrollView backgroundColor={authenticated ? 'lightgreen' : 'pink'}>
    <stackLayout class="p-20">
      <wrapLayout>
        {#if authenticated}
          <button on:tap={() => authenticated = false}>Logout</button>
        {:else}
          <button on:tap={promptForLogin}>Login ...</button>
        {/if}
      </wrapLayout>

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

      <image src="~/svelte-native-logos.png" stretch="fill" />

      <segmentedBar selectedBackgroundColor="yellow">
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

      <label class="panagram" textWrap="true">
        <formattedString>
          <span class="fox">The quick brown fox</span>
          <span text=" jumps over " />
          <span class="dog">the lazy dog</span>
          <span text="." />
        </formattedString>
      </label>

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

      <button on:tap={startProgress}>Start Progress</button>
      <progress class="progress" maxValue={100} value="{progressPercent}" />

      <wrapLayout>
        <label text="Birthday" />
        <datePicker bind:date={birthday} />
        <label class="plain" text="You selected {formatDate(birthday)}." />
      </wrapLayout>
      <wrapLayout>
        <listView items={colors} on:itemTap={onTapColor}>
          <Template let:item={color}>
            <label class="list" text="One of the colors is {color}." />
          </Template>
        </listView>
      </wrapLayout>
      <wrapLayout>
        <label text="Favorite Color" />
        <!--TODO: See https://github.com/halfnelson/svelte-native/issues/129 -->
        <!--listPicker items={colors} bind:selectedIndex={favoriteColorIndex} 
          on:selectedIndexChange={onFavoriteColor} /-->
        <listPicker items={colors} on:selectedIndexChange={onFavoriteColor} />
        <label class="plain" text="You selected {favoriteColor}." />
      </wrapLayout>
      <button on:tap={pickColor}>Pick a Color</button>
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
      <wrapLayout>
        <label text="Like Running?" />
        <switch bind:checked={likeRunning} />
        <label
          class="plain"
          text="You{likeRunning ? '' : ' do not'} like running."
        />
      </wrapLayout>
      <wrapLayout>
        <label text="First Name" />
        <textField class="first-name" bind:text={firstName} />
        <label class="plain" text="Your first name is {firstName}." />
      </wrapLayout>
      <button on:tap={getFirstName}>Prompt for First Name</button>
      <wrapLayout>
        <label text="What would you say you do here?" />
        <textView class="reason" bind:text={reason} />
        <label class="plain" text="Your reason for being is {reason}." textWrap="true" />
      </wrapLayout>
      <wrapLayout>
        <label text="Quitting Time" />
        <timePicker bind:time={quittingTime} />
        <label class="plain" text="You will quit at {formatTime(quittingTime)}." />
      </wrapLayout>

      <!--tabs bind:selectedIndex={selectedTab}>

        !-- The bottom tab UI is created via TabStrip (the container)
             and TabStripItem (for each tab). --
        <tabStrip>
          <tabStripItem>
            <label text="Home" />
            <image src="font://&#xf015;" class="fas t-36" />
          </tabStripItem>
          <tabStripItem class="special">
            <label text="Account" />
            <image src="font://&#xf007;" class="fas t-36" />
          </tabStripItem>
          <tabStripItem class="special">
            <label text="Search" />
            <image src="font://&#xf00e;" class="fas t-36" />
          </tabStripItem>
        </tabStrip>

        !-- The number of TabContentItem components should
             correspond to the number of TabStripItem components --
        <tabContentItem>
          <gridLayout>
            <label text="Home Page" class="h2 text-center" />
          </gridLayout>
        </tabContentItem>
        <tabContentItem>
          <gridLayout>
            <label text="Account Page" class="h2 text-center" />
          </gridLayout>
        </tabContentItem>
        <tabContentItem>
          <gridLayout>
            <label text="Search Page" class="h2 text-center" />
          </gridLayout>
        </tabContentItem>

      </tabs-->
    </stackLayout>
  </scrollView>
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
  }

  webView {
    border-color: red;
    border-width: 1;
    /*height: 50; /*TODO: Is this required? */
  }
</style>
