<style lang="stylus" scoped>
article
  header
    h2
      font-weight: 100
      font-size: 1em
      color: #66
  table
    display: table
    width: 100%
    background: #fefefe
    border-collapse: collapse
    tr
      border-bottom: 1px solid #ee
      td
        padding: 24px 2px 20px 2px
        vertical-align: top
        color: #66
        &.key
          color: #77
          font-size: .9em
          font-weight: bold
        &.val
          text-align: right
  h3
    margin-bottom: 10px
    font-size: .9em
    font-weight: 600
    color: #88
    text-transform: uppercase
  .switch
    position: absolute
    top: 2.1rem
    right: 1.6rem
    display: block
    width: 5.4rem
    height: 2.6rem
    border-radius: 2.4rem
    background-color: #BDC6C8
    transition: all .2s
    overflow: hidden

    .tick
      display: none
      position: absolute
      height: 1.4rem
      left: .6rem
      top: .6rem
    .ball
      position: absolute
      display: block
      top: -.35rem
      left: -.3rem
      width: 2rem
      height: 2rem
      border: .7rem solid #ABB5B7
      border-radius: 50%
      background-color: #FFF
      transition: all .2s
      box-sizing: content-box

    &[data-paused='true']
      background-color: #2EDF88
      .tick
        display: block
      .ball
        left: 2.4rem
        border-color: #2CC269

</style>

<template lang="jade">
article.form.policy(transition="driftFade")
  header
    .switch(@click='pause', data-paused="{{(!paused).toString()}}")
      .ball
      img.tick(src='img/tick.svg')
    h1.
      #[strong #[em "{{policy.name}}"]] policy stats

    h2.
      Instance of #[a.cool(@click='openExternal', href='{{policy.uri}}') {{policy.uri}}]
  table(v-if='policy.params')
    tr(v-for='(key, val) of policy.params')
      td.key {{key}}
      td.val {{val}}
</template>

<script lang="coffee">
app = document.app
module.exports =
  data: ->
    paused: @$parent.policies[decodeURIComponent @$route.params.policy].paused
  computed:
    index: -> decodeURIComponent @$route.params.policy
    events: -> @$parent.events
    policy: -> @$parent.policies[@index]
  methods:
    openExternal: (e) ->
      e.preventDefault()
      url = $(e.target).attr 'href'
      window.electron.shell.openExternal url
    pause: (e) ->
      @paused = not @paused
      @policy.paused = @paused
      document.vault.replace 'settings', $.extend(@$parent.watcher.settings, {encrypt: true})
      app.save()
</script>
