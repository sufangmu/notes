<style>
    /* 定制公告栏音乐插件的样式 */
    .aplayer {
        font-family: Arial,Helvetica,sans-serif;  /*音乐插件字体*/
        margin: 0px;  /*音乐插件与公告栏左边的边距，0px就是直接抵到公告栏左边的边上*/
        box-shadow: 0 2px 2px 0 rgba(0,0,0,.14), 0 3px 1px -2px rgba(0,0,0,.2), 0 1px 5px 0 rgba(0,0,0,.12);
        border-radius: 2px;
        overflow: hidden;
        -webkit-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        line-height: normal;
    }
</style>
<!-- 为博客底部添加音乐组件 -->
<div id="player"  class="aplayer"></div>
<link href="../static/APlayer.min_v1.10.1.css" rel="stylesheet">

<script src="../static/APlayer.min_v1.10.1.js"></script> 
<script type="text/javascript">
var ap = new APlayer({
    element: document.getElementById('player'),
    narrow: false,
    autoplay: false,          <!-- 是否自动播放 -->
    showlrc: false,
    theme: '#cdd9e1',      <!-- 插件背景颜色，建议和你的公告栏背景色一样，这样融为一体的感觉 -->
    music: [
        {
            title: '平湖秋月',
            author: '罗守诚',
            url: '../xiao/罗守诚 - 平湖秋月(箫).mp3',
            pic: ''
        },
        {
            title: '晴雯曲',
            author: '谭炎健',
            url: '../xiao/谭炎健 - 晴雯曲.mp3',
            pic: ''
        },
    ]
});
ap.init();
</script>