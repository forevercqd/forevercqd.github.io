---
title: Texture纹理下载
catalog: true
date: 2020-05-23 23:38:30
subtitle:
header-img:
tags:
- OpenGL
---


# Texture纹理下载
## java 版本
```
int dumpTextureToBmp(int textureId, int txtWidth, int txtHeight, String dumpFilePath) {
    // 获取原来绑定的FBO
    IntBuffer oldFbo = IntBuffer.allocate(1);
    GLES20.glGetIntegerv(GLES20.GL_FRAMEBUFFER_BINDING, oldFbo);

    // 创建 FBO
    int[] fbo = new int[2];
    GLES20.glGenFramebuffers(1, fbo, 0);
    // 绑定新的帧缓冲区对象
    GLES20.glBindFramebuffer(GL_FRAMEBUFFER, fbo[0]);

    // 绑定纹理至新建的FBO
    GLES20.glFramebufferTexture2D(GL_FRAMEBUFFER, GLES20.GL_COLOR_ATTACHMENT0, GLES20.GL_TEXTURE_2D, textureId, 0);

    // check
    int status = GLES20.glCheckFramebufferStatus(GL_FRAMEBUFFER);
    if (status != GLES20.GL_FRAMEBUFFER_COMPLETE) { //　error:  GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT
        Log.e(AliyunTag.TAG, "cqd, glCheckFramebufferStatus, status = " + status);
        return -1;
    }

    // 创建存储纹理下载后的数据
    ByteBuffer mRGBABuffer = ByteBuffer.allocateDirect(txtWidth * txtHeight * 4);

    GLES20.glViewport(0, 0, txtWidth, txtHeight);
    // 下载纹理
    if (mRGBABuffer != null) {
        GLES20.glReadPixels(0, 0, txtWidth, txtHeight, GLES20.GL_RGBA, GLES20.GL_UNSIGNED_BYTE, mRGBABuffer);
        mRGBABuffer.rewind();
    }

    try {
        FileOutputStream fos = new FileOutputStream(new File(dumpFilePath));

        Bitmap srcBitmap = Bitmap.createBitmap(txtWidth, txtHeight, Bitmap.Config.ARGB_8888);       // cqd.
        srcBitmap.copyPixelsFromBuffer(mRGBABuffer);
        srcBitmap.compress(Bitmap.CompressFormat.JPEG, 100, fos);

        fos.flush();
        fos.close();
        srcBitmap.recycle();
    } catch (Exception e) {
        Log.e(AliyunTag.TAG, "cqd,  createBitmap，　Exception = " + e);
        return -2;
    }

    // 还原　FBO
    GLES20.glBindFramebuffer(GL_FRAMEBUFFER, oldFbo.get());
    GLES20.glDeleteFramebuffers(1, fbo, 0);

    return 0;
}
```


## C++ 版本
```
int dumpTextureToBmp(int textureId, int txtWidth, int txtHeight, char *dumpFilePath)
{
    // 获取原来绑定的FBO
    GLint oldFbo[2] = {0};
    glGetIntegerv(GL_FRAMEBUFFER_BINDING, oldFbo);

    // 创建 FBO
    GLuint fbo[2] = {0};
    glGenFramebuffers(1, fbo);
    // 绑定新的帧缓冲区对象
    glBindFramebuffer(GL_FRAMEBUFFER, fbo[0]);

    // 绑定纹理至新建的FBO
    glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, textureId, 0);

    // check
    int status = glCheckFramebufferStatus(GL_FRAMEBUFFER);
    if (status != GL_FRAMEBUFFER_COMPLETE)   //　error:  GL_FRAMEBUFFER_INCOMPLETE_MISSING_ATTACHMENT
    {
        SRLOGE(SVIDEO_RENDER_TAG, "cqd, glCheckFramebufferStatus, status = " + status);
        return -1;
    }

    // 创建存储纹理下载后的数据
    u_char *mRGBABuffer = new u_char[txtWidth * txtHeight * 4];

    glViewport(0, 0, txtWidth, txtHeight);
    // 下载纹理
    if (mRGBABuffer != nullptr)
    {
        glReadPixels(0, 0, txtWidth, txtHeight, GL_RGBA, GL_UNSIGNED_BYTE, mRGBABuffer);
    }

    FILE *fp = fopen(dumpFilePath, "wb");
    if (fp)
    {
        fwrite(mRGBABuffer, sizeof(u_char), txtWidth * txtHeight * 4, fp);
        fclose(fp);
    }

    // 还原　FBO
    glBindFramebuffer(GL_FRAMEBUFFER, oldFbo[0]);
    glDeleteFramebuffers(1, fbo);

    delete []mRGBABuffer;

    return 0;
}
```
