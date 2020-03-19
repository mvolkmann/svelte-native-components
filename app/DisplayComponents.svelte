<script>
  import {backgroundColor} from './stores';

  const myHtml = `
    <div>
      <span style="color: red">The quick brown fox</span>
      <span>jumps over</span>
      <span style="color: blue">the lazy dog</span>
    </div>
  `;
  //const myHtml = '<h1 style="color: red">Test</h1>';

  let progressPercent = 0;

  function startProgress() {
    progressPercent = 0;
    const token = setInterval(() => {
      progressPercent++;
      if (progressPercent === 100) clearInterval(token);
    }, 10);
  }
</script>

<scrollView>
  <stackLayout
    backgroundColor={$backgroundColor}
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

<style>
  .dog {
    color: blue;
    font-style: italic;
  }

  .fox {
    color: red;
    font-weight: bold;
  }

  image {
    border-color: green;
    border-width: 1;
  }

  .panagram {
    background-color: linen;
    color: black;
    font-size: 26;
    font-weight: normal;
    margin-bottom: 20;
    padding: 20;
  }

  progress {
    color: red;
    margin-bottom: 10;
    scale-y: 5; /* changes height */
  }

  webView {
    border-color: red;
    border-width: 1;
    /*height: 50; /*TODO: Is this required? */
  }
</style>