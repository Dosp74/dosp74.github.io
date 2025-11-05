---
title: "[컴퓨터 비전] Interpolation - Bilinear Interpolation 구현하기"
date: 2025-10-31 15:42:00 +0900
last_mod: 2025-11-06
categories: [컴퓨터 비전]
tags:
  [컴퓨터 비전, opencv, Visual Studio, Interpolation, Bilinear Interpolation]
---

![Image](/assets/images/2025-10-31/2025-10-31-image-interpolation.png)

영상을 확대, 축소하는 등 해상도를 바꾸고 싶을 때 Interpolation, 우리말로 보간법이라는 기법을 사용하는데, 이에 대한 예로 Replication, Nearest Neighbor, Linear Interpolation 등이 있다.

하나씩 알아보자.

## Replication

![Image](/assets/images/2025-10-31/2025-10-31-replication.png)

Image Interpolation의 가장 간단한 방법으로 Replication이 있다.

Replication은 영상을 N배로 키우는 것이고, 중간에 필요한 픽셀들을 그대로 복사해서 사용한다. 이 방법에서 아쉬운 점은 비효율적이라는 것이다. 자기 자신 기준으로 가장 가까운 픽셀을 복사하는 것이 좀 더 합리적이다.

이미지에서 파란색 화살표로 가리킨 픽셀은 오른쪽이 더 가깝지만 왼쪽을 복사한다. 이에 대한 해결 방법(?)이 Nearest Neighbor이다.

## Nearest Neighbor

![Image](/assets/images/2025-10-31/2025-10-31-nearest-neighbor.png)

원본 픽셀 근처의 픽셀을 복사하지만, 마찬가지로 N개의 픽셀이 묶이기 때문에 큰 차이는 없다.

이러한 방법을 사용했을 때는 픽셀의 크기가 커지기만 하지, 디테일들이 부드럽게 연결되지는 않는다.

## Linear Interpolation

![Image](/assets/images/2025-10-31/2025-10-31-linear-interpolation-1.png)

Linear Interpolation(선형 보간)은 두 점을 잇는 선분 사이의 어떤 특정 위치에 대한 픽셀의 밝기 값을 구할 때 사용한다. 지금까지 나온 보간법 중 가장 합리적인 것 같다.

1차원 축에서 두 점 사이의 값을 보간하는 것인데, 중간에 필요한 픽셀들을 적절한 픽셀 값으로 정하여 넣어준다.

![Image](/assets/images/2025-10-31/2025-10-31-linear-interpolation-2.png)

여기서 q는 밝기 값을 의미하며, 두 삼각형의 닮음비를 활용하여 Linear Interpolation에서 q에 대한 밝기 값을 위 그림처럼 유도할 수 있다.

## Bilinear Interpolation

![Image](/assets/images/2025-10-31/2025-10-31-bilinear-interpolation.png)

Linear Interpolation을 응용하여 2차원 격자 위의 네 점 사이의 어떤 특정 위치에 대한 픽셀의 밝기 값을 최대한 보정하면서 구하려고 할 때 사용하는 기법이 **Bilinear Interpolation** 이다.

Linear Interpolation을 3번 반복하면 네 점 사이의 특정 위치에 대한 픽셀의 밝기 값을 구할 수 있다.

p1, p2로 q1을 구하고, p3, p4로 q2를 구한 후 q1, q2로 q를 구하면 된다.

<br>

다음은 256x256 크기의 Lena_256x256.png 파일을 436x436, 512x512 크기의 영상으로 확대한 결과이다.

![Image](/assets/images/2025-10-31/2025-10-31-bilinear-interpolation-example.png)

[소스 코드]

사용 언어: C++

환경: Microsoft Windows 10 Home, Visual Studio 2022, C:\opencv\...

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
#include <cmath>
using namespace cv;
using namespace std;

Mat bilinearInterpolation(const Mat& src, int newW, int newH) {
    Mat result(newH, newW, CV_8UC1); // CV_8UC1 = 0 ~ 255 값의 1채널 이미지(매크로/타입/채널)

    // scaleX, scaleY = 출력 픽셀 한 칸이 입력 픽셀의 몇 칸 분량인지
    double scaleX = static_cast<double>(src.cols) / newW;
    double scaleY = static_cast<double>(src.rows) / newH;

    // x, y = 출력 픽셀 인덱스, srcX, srcY = 원본 이미지 좌표(실수)
    for (int y = 0; y < newH; y++) {
        double srcY = y * scaleY;
        int y0 = static_cast<int>(floor(srcY)); // srcY 위 픽셀
        int y1 = min(y0 + 1, src.rows - 1); // srcY 아래 픽셀
        double dy = srcY - y0; // 위쪽 픽셀로부터 얼마나 떨어져 있는지

        for (int x = 0; x < newW; x++) {
            double srcX = x * scaleX;
            int x0 = static_cast<int>(floor(srcX)); // srcX 왼쪽 픽셀
            int x1 = min(x0 + 1, src.cols - 1); // srcX 오른쪽 픽셀
            double dx = srcX - x0; // 왼쪽 픽셀로부터 얼마나 떨어져 있는지

            uchar p1 = src.at<uchar>(y0, x0); // 밝기 값(0 ~ 255)
            uchar p2 = src.at<uchar>(y0, x1);
            uchar p3 = src.at<uchar>(y1, x0);
            uchar p4 = src.at<uchar>(y1, x1);

            // 공식 적용
            double q1 = (1 - dx) * p1 + dx * p2;
            double q2 = (1 - dx) * p3 + dx * p4;
            double q = (1 - dy) * q1 + dy * q2; // 최종 밝기 값

            result.at<uchar>(y, x) = static_cast<uchar>(q);
        }
    }

    return result;
}

int main() {
    Mat src = imread("Lena_256x256.png", IMREAD_GRAYSCALE); // 그레이스케일(1채널)

    if (src.empty()) {
        cout << "이미지를 열 수 없습니다." << endl;

        return -1;
    }

    Mat Lena_436x436 = bilinearInterpolation(src, 436, 436);
    Mat Lena_512x512 = bilinearInterpolation(src, 512, 512);

    imshow("source", src);
    imshow("436x436", Lena_436x436);
    imshow("512x512", Lena_512x512);

    waitKey(0);

    return 0;
}
```

이미지 출처

광운대학교 이윤구 교수님 강의자료  
Rafael C. Gonzalez & Richard E. Woods, Digital Image Processing (2nd Edition, Prentice Hall)
