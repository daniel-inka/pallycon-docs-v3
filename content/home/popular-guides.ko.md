+++
# A Demo section created with the Blank widget.
# Any elements can be added in the body: https://sourcethemes.com/academic/docs/writing-markdown-latex/
# Add more sections by duplicating this file and customizing to your requirements.

widget = "blank"  # See https://sourcethemes.com/academic/docs/page-builder/
headless = true  # This file represents a page section.
active = true  # Activate this widget? true/false
weight = 40  # Order that this section will appear.

title = "주요 문서"
subtitle = "고객들이 가장 많이 참고하고 있는 주요 가이드 문서들입니다."

[design]
  # Choose how many columns the section has. Valid values: 1 or 2.
  columns = "2"

[design.background]
  # Apply a background color, gradient, or image.
  #   Uncomment (by removing `#`) an option to apply it.
  #   Choose a light or dark text color by setting `text_color_light`.
  #   Any HTML color name or Hex value is valid.

  # Background color.
  # color = "navy"
  
  # Background gradient.
  # gradient_start = "DeepSkyBlue"
  # gradient_end = "SkyBlue"
  
  # Background image.
#  image = "headers/bubbles-wide.jpg"  # Name of image in `static/media/`.
#  image_darken = 0.6  # Darken the image? Range 0-1 where 0 is transparent and 1 is opaque.
#  image_size = "cover"  #  Options are `cover` (default), `contain`, or `actual` size.
#  image_position = "center"  # Options include `left`, `center` (default), or `right`.
#  image_parallax = true  # Use a fun parallax-like fixed background effect? true/false

  # Text color (true=light or false=dark).
  text_color_light = false

[design.spacing]
  # Customize the section spacing. Order is top, right, bottom, left.
  padding = ["20px", "0", "20px", "0"]

[advanced]
 # Custom CSS. 
 css_style = ""
 
 # CSS class.
 css_class = ""
+++

{{% callout info %}}
**멀티DRM 라이선스 토큰 가이드**

멀티DRM 콘텐츠의 재생에 필요한 DRM 라이선스 발급 방식인 `라이선스 토큰`의 생성 규격에 대한 가이드 문서입니다.

[**바로 가기**](/ko/multidrm/license/license-token)
{{% /callout %}}

{{% callout note %}}
**HTML5 플레이어 연동 가이드**

서비스 사이트의 웹 페이지에서 HTML5 플레이어를 이용해 멀티DRM(PlayReady, Widevine, FairPlay Streaming)으로 보호된 스트리밍 콘텐츠(DASH 또는 HLS)를 재생하는 방법을 설명합니다.

[**바로 가기**](/ko/multidrm/clients/html5-player/)
{{% /callout %}}

{{% callout note %}}
**포렌식 워터마킹 연동 워크플로우**

 PallyCon 포렌식 워터마킹 연동에 필요한 전체 워크플로우와 단계별 각종 옵션들을 설명합니다.

[**바로 가기**](/ko/forensic-watermarking/getting-started/watermarking-workflow/)
{{% /callout %}}
