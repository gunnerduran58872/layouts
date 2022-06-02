<template lang="pug">
  div
    cookie-banner
    tm-top-banner(v-bind="{topBanner}")
    .layout
      .layout__sidebar
        tm-sidebar-content(:value="tree" :tree="directoryTree")
      .layout__main
        .layout__main__navbar
          tm-top-bar(@sidebar="sidebarVisible = $event" @search="searchPanel = $event")
        .layout__main__content(:class="[`aside__${!($frontmatter.aside === false)}`]")
          .layout__main__content__body(id="content-scroll")
            .layout__main__content__body__breadcrumbs(v-if="!($frontmatter.aside === false)")
              tm-breadcrumbs(@visible="rsidebarVisible = $event")
            .layout__main__content__body__wrapper
              component(:is="layout" :key="$route.path" @search="searchPanel = $event" @prereq="prereq = $event")
                Content
          .layout__main__content__aside__container(v-if="!($frontmatter.aside === false)" :style="{'--height-banners': heightBanners + 'px'}")
            .layout__main__content__aside(:class="[`aside__bottom__${!!asideBottom}`]")
              client-only
                tm-aside(id="aside-scroll" @search="searchPanel = $event" @bannerError="asideBanners = null" v-bind="{asideBanners, asideBannersUrl, prereq}")
            .layout__main__content__aside__banners(ref="banners" v-if="editLink")
              a(:href="editLink" target="_blank")
                card-banner
        .layout__main__gutter(v-if="!($frontmatter.aside === false)")
          tm-footer-links(:tree="tree")
        .layout__main__footer
          tm-footer(:tree="directoryTree" :full="$page.frontmatter.footer && $page.frontmatter.footer.newsletter === false")
    tm-sidebar(:visible="sidebarVisible" @visible="sidebarVisible = $event").sheet__sidebar
      tm-sidebar-content(:value="tree" :tree="directoryTree" :compact="true")
    tm-sidebar(:visible="searchPanel" @visible="searchPanel = $event" max-width="100vw" width="480px" side="right" box-shadow="0 0 50px 10px rgba(0,0,0,.1)" background-color="rgba(0,0,0,0)").sheet__sidebar
      section-search(@visible="searchPanel = $event" :base="$site.base" @cancel="searchPanel = false" :algoliaConfig="algoliaConfig" @select="searchSelect($event)" :query="searchQuery" @query="searchQuery = $event" :site="$site")
    tm-sidebar(:visible="rsidebarVisible"  @visible="rsidebarVisible = $event" side="right").sheet__sidebar.sheet__sidebar__toc
      tm-toc-menu(@visible="rsidebarVisible = $event")
</template>

<style lang="stylus" scoped>
.sheet
  &__sidebar
    z-index 10000
    position relative
    scrollbar-color #eee white
    &__toc
      display none
.layout__main__content__aside.aside__bottom__true
  position absolute
  bottom var(--height-banners, 0)
  right 0
  max-height initial
  top initial
  height initial
.layout__main__content.aside__false
  display block
.layout
  display grid
  width 100%
  grid-template-columns var(--sidebar-width) calc(100% - var(--sidebar-width))
  max-width var(--layout-max-width, 1540px)
  margin-left auto
  margin-right auto
  position relative
  &__sidebar
    position sticky
    top 0
    height 100vh
    overflow-y scroll
    scrollbar-color #eee white
    &::-webkit-scrollbar
      background rgba(255, 255, 255, 0)
      width 6px
      transition background 0.25s
    &::-webkit-scrollbar-thumb
      background rgba(255, 255, 255, 0)
      border-radius 6px
      transition background 0.25s
    &:hover
      &::-webkit-scrollbar
        background rgba(255, 255, 255, 0)
      &::-webkit-scrollbar-thumb
        background #eee
  &__main
    &__navbar
      padding-left 2.5rem
      padding-right 2.5rem
      display none
      position sticky
      top 0
      width 100%
      background white
      z-index 500
    &__content
