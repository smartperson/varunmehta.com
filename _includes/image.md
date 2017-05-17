{% if site.developer_mode %}
  ![]({{include.image_url}})
{% else %}
  {% cloudinary {{include.image_url}} %}
{% endif %}