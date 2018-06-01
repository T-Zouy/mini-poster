<template>
  <div class="container">
    <canvas canvas-id="shareCanvas" style="width:370px;height:600px"></canvas>
    <div class="footer" @click="generatePoster">海报生成</div>
  </div>
</template>

<script>
  import promisify from '../../utils/promisify'
  export default {
    name: 'index',
    data () {
      return {}
    },
    mounted () {
      const wxGetImageInfo = promisify(wx.getImageInfo)

      Promise.all([
        wxGetImageInfo({
          src: 'http://qnm.hunliji.com/o_1ceavhf3n5gcl80h4r1sak15kj7.png'
        }),
        wxGetImageInfo({
          src: 'http://qnm.hunliji.com/o_1ceavt3dfjug1ijn1r07vre1ojd.png'
        })
      ]).then(res => {
        console.log(11111111111)
        const ctx = wx.createCanvasContext('shareCanvas')

        // 底图
        ctx.drawImage(res[0].path, 0, 0, 370, 600)

        // 作者名称
        ctx.setTextAlign('center')
        ctx.setFillStyle('#000000')
        ctx.setFontSize(22)
        ctx.fillText('test the poster', 370 / 2, 400)

        ctx.setFontSize(14)
        ctx.setFillStyle('#333333')
        ctx.setTextAlign('center')
        ctx.font = 'bold 30px Source-Han-Ligh125f9384b21d11'
        ctx.fillText('邀请你一起去吃面', 185, 370)

        ctx.setFontSize(14)
        ctx.setFillStyle('#333333')
        ctx.setTextAlign('center')
        ctx.font = 'bold 30px Glyphicons Halflings'
        ctx.fillText('字体为: Calibri', 111, 100)

        // 小程序码
        const qrImgSize = 180
        ctx.drawImage(res[1].path, (600 - qrImgSize) / 2, 430, qrImgSize, qrImgSize)

        ctx.stroke()
        ctx.draw()
      }).catch(e => {
        console.log('error msg: ')
        console.log(e)
      })
    },
    methods: {
      generatePoster () {
        const wxCanvasToTempFilePath = promisify(wx.canvasToTempFilePath)
        const wxSaveImageToPhotosAlbum = promisify(wx.saveImageToPhotosAlbum)

        wxCanvasToTempFilePath({
          canvasId: 'shareCanvas'
        }, this).then(res => {
          return wxSaveImageToPhotosAlbum({
            filePath: res.tempFilePath
          })
        }).then(res => {
          wx.showToast({
            title: '已保存到相册'
          })
        })
      }
    }
  }
</script>

<style scoped>
  @font-face {
    font-family: 'Source-Han-Ligh125f9384b21d117';
    src: url('https://cdn.webfont.youziku.com/webfonts/nomal/119063/46865/5b110092f629d90c404435b7.gif?r=76234514656');
    src: url('https://cdn.webfont.youziku.com/webfonts/nomal/119063/46865/5b110092f629d90c404435b7.gif?r=76234514656?#iefix') format('embedded-opentype'),url('//cdn.webfont.youziku.com/webfonts/nomal/119063/46865/5b110092f629d90c404435b7.png?r=76234514656') format('woff2'),url('//cdn.webfont.youziku.com/webfonts/nomal/119063/46865/5b110092f629d90c404435b7.bmp?r=76234514656') format('woff'),url('//cdn.webfont.youziku.com/webfonts/nomal/119063/46865/5b110092f629d90c404435b7.jpg?r=76234514656') format('truetype');
    font-weight: normal;
    font-style: normal;
  }

  .container {
    position: relative;
  }

  .footer {
    position: absolute;
    top: 0;
    left: 0;
    width: 375px;
    height: 47px;
    background-color: blueviolet;
    color: #fff;
    font-family: "Source-Han-Ligh125f9384b21d117";
    font-size: 16px;
    line-height: 22px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
