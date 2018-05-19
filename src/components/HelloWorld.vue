<template>
  <div class="hello">
    <h1><span>T</span> <span>U</span> <span>I</span></h1>
    <h2>Essential Links</h2>
    <ul>
      <li>
        <a
          href="http://www.vuetui.com"
          target="_blank"
        >
          Core Docs
        </a>
      </li>
      <li>
        <a
          href="https://github.com/Bennnis/TUI"
          target="_blank"
        >
          Github
        </a>
      </li>
      <t-tooltip position="right" theme="dark" content="微信: HMT19940210">
        <li>
          <a
            href="javascript:void(0)"
            target="_blank"
          >
            Contact
          </a>
        </li>
      </t-tooltip>
    </ul>
    <h2>Ecosystem</h2>
    <ul>
      <li>
        <a
          href="https://github.com/Bennnis/tui-cli/"
          target="_blank"
        >
          tui-cli
        </a>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: 'T U I'
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  font-family: 'Apple Chancery', Helvetica, Arial, sans-serif;
  font-size: 40px;
  margin-bottom: 20px;
}

h1 span:first-child {
  color: #2ED573
}

h1 span:nth-child(2) {
  color: #44A9FF;
}
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #44A9FF;
}
</style>
