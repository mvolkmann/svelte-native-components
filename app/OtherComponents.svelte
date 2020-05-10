<script>
  import {login} from 'tns-core-modules/ui/dialogs'
  import {
    authenticated,
    backgroundColor,
    colors,
    favoriteColor,
    favoriteColorIndex,
    firstName
  } from './stores';

  let busy = false;
  let query = '';

  // Should this be handled in stores.js?
  $: $backgroundColor = $authenticated ? 'lightgreen' : 'pink';

  async function getFirstName() {
    const res = await prompt({
      title: 'First Name',
      message: 'Please tell me your first name.',
      okButtonText: 'Here it is',
      cancelButtonText: 'I will not share that'
    });
    if (res.result) $firstName = res.text;
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

  async function pickColor() {
    const NONE = 'No Thanks';
    const choice = await action('Pick a color', NONE, colors);
    if (choice !== NONE) {
      $favoriteColorIndex = colors.findIndex(c => c === choice);
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
      $authenticated = res.userName === 'foo' && res.password === 'bar';
      if (!$authenticated) {
        alert({
          title: 'Login Failed',
          message: 'Your username or password was incorrect.',
          okButtonText: 'Bummer'
        });
      }
    }
  }
</script>

<scrollView>
  <stackLayout
    backgroundColor={$backgroundColor}
    class="p-20"
  >
    <label class="title" text="login" />
    <wrapLayout>
      {#if $authenticated}
        <button on:tap={() => $authenticated = false}>Logout</button>
      {:else}
        <button on:tap={promptForLogin}>Login ...</button>
      {/if}
    </wrapLayout>

    <label class="title" text="prompt" />
    <button on:tap={getFirstName}>Prompt for First Name</button>
    <label class="plain" text="Your first name is {$firstName}." />

    <label class="title" text="action" />
    <button on:tap={pickColor}>Pick a Color</button>
    <label class="plain" text="You selected {$favoriteColor}." />

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

<style>
  activityIndicator {
    color: blue;
  }

  searchBar {
    margin-bottom: 10;
  } 
</style>
