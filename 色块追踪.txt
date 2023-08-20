import cv2
import numpy as np
import time
cap = cv2.VideoCapture(0)
def ShapeDetection(img):
    contours,hierarchy = cv2.findContours(img,cv2.RETR_EXTERNAL,cv2.CHAIN_APPROX_NONE)  #寻找轮廓点
    for obj in contours:
        area = cv2.contourArea(obj)  #计算轮廓内区域的面积
    #    if area < 10000:
    #    continue
        cv2.drawContours(imgContour, obj, -1, (255, 0, 0), 4)  #绘制轮廓线
        # 4.获取最小外接圆 圆心 半径
        center, radius = cv2.minEnclosingCircle(obj[0])
        center = np.intp(center)
        print(center)
        # 5.绘制最小外接圆
        #img_result = imgContour
        #cv2.circle(img_result, tuple(center), int(radius), (255, 255, 255), 2)



if __name__ == "__main__":
    while True:
        ret, frame = cap.read()  # 从摄像头中读取一帧图像
        img = frame
        imgContour = img.copy()
        # 在彩色图像的情况下，解码图像将以b g r顺序存储通道。
        grid_RGB = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

        # 从RGB色彩空间转换到HSV色彩空间
        grid_HSV = cv2.cvtColor(grid_RGB, cv2.COLOR_RGB2HSV)

        # red H、S、V范围一：
        red_lower1 = np.array([0, 43, 46])
        red_upper1 = np.array([10, 255, 255])
        mask1 = cv2.inRange(grid_HSV, red_lower1, red_upper1)  # mask1 为二值图像
        res1 = cv2.bitwise_and(grid_RGB, grid_RGB, mask=mask1)

        # red H、S、V范围二：
        red_lower2 = np.array([156, 43, 46])
        red_upper2 = np.array([180, 255, 255])
        mask2 = cv2.inRange(grid_HSV, red_lower2, red_upper2)
        res2 = cv2.bitwise_and(grid_RGB, grid_RGB, mask=mask2)

        # green H、S、V范围
        green_lower = np.array([35, 43, 46])
        green_upper = np.array([77, 255, 255])
        mask4 = cv2.inRange(grid_HSV, green_lower, green_upper)
        res4 = cv2.bitwise_and(grid_RGB, grid_RGB, mask=mask4)

        # 将两个二值图像结果 相加
        mask3 = mask1 + mask2

        close = cv2.morphologyEx(mask4, cv2.MORPH_CLOSE, (3, 3), iterations=5)
        # 结果显示
        ShapeDetection(close)  # 形状检测
        cv2.imshow("mask3", imgContour)


        cv2.waitKey(1000)
        cv2.destroyAllWindows()