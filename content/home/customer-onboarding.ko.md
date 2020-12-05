+++
# A Demo section created with the Blank widget.
# Any elements can be added in the body: https://sourcethemes.com/academic/docs/writing-markdown-latex/
# Add more sections by duplicating this file and customizing to your requirements.

widget = "blank"  # See https://sourcethemes.com/academic/docs/page-builder/
headless = true  # This file represents a page section.
active = true  # Activate this widget? true/false
weight = 30  # Order that this section will appear.

title = "고객 온보딩 가이드"
#subtitle = "고객사의 다양한 상황과 요구사항에 맞는 가이드 문서를 쉽게 찾을 수 있도록 주요 제품인 멀티DRM과 포렌식 워터마킹에 대한 온보딩 가이드를 제공합니다."

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
  image = "headers/welcome-onboard-unsplash.jpg"  # Name of image in `static/media/`.
  image_darken = 0.4  # Darken the image? Range 0-1 where 0 is transparent and 1 is opaque.
  image_size = "cover"  #  Options are `cover` (default), `contain`, or `actual` size.
  image_position = "center"  # Options include `left`, `center` (default), or `right`.
  image_parallax = true  # Use a fun parallax-like fixed background effect? true/false

  # Text color (true=light or false=dark).
  text_color_light = true

[design.spacing]
  # Customize the section spacing. Order is top, right, bottom, left.
  padding = ["20px", "0", "20px", "0"]

[advanced]
 # Custom CSS. 
 css_style = ".section-heading p {color: #FFF;}"
 
 # CSS class.
 css_class = ""
+++

{{% callout info %}}
**멀티DRM 온보딩 가이드**

콘텐츠 패키징과 라이선스 발급 연동 등 PallyCon 멀티DRM 서비스의 적용 과정을 단계별로 설명하고 관련 가이드로 이동할 수 있게 안내하는 문서입니다.

[**바로 가기**](./multidrm/getting-started/multidrm-onboarding)
{{% /callout %}}

{{% callout note %}}
**포렌식 워터마킹 온보딩 가이드**

PallyCon 포렌식 워터마킹 서비스를 적용하기 위해 필요한 워터마크 전처리와 삽입 등의 연동 과정을 설명하고 관련 가이드로 안내하는 문서입니다.

[**바로 가기**](./forensic-watermarking/getting-started/fwm-onboarding)
{{% /callout %}}
