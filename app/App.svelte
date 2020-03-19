<script>
  import DisplayComponents from './DisplayComponents.svelte';
  import InputComponents from './InputComponents.svelte';
  import OtherComponents from './OtherComponents.svelte';

  let selectedTab;

  function onTapDelete() {
    console.log('App.svelte onTapDelete: entered');
  }

  function onTapShare() {
    console.log('App.svelte onTapShare: entered');
  }
</script>

<page>
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

    <tabContentItem>
      <DisplayComponents />
    </tabContentItem>

    <tabContentItem>
      <InputComponents />
    </tabContentItem>

    <tabContentItem>
      <OtherComponents />
    </tabContentItem>
  </tabs>
</page>
