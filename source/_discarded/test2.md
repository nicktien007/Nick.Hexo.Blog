title: test2
author: Nick
tags:
  - test
  - test1
  - test2
  - test3
  - test4
  - test5
  - test6
  - test7
  - test8
  - test9
  - test10
  - test11
  - test12
  - test13
  - test14
  - test15
  - test16
  - test17
  - test18
  - test19
  - test20
categories: []
date: 2021-01-01 00:58:00
---
# 測試我

{% note success %}
#### Success Header
{% endnote %}

```java=
@SneakyThrows
    public List<GamePublishTopicDTO> getTopicGameInfo(boolean isMobile,List<String> labels) {
        String key = "";
        if (isMobile){
            key = "gamepublish:" + gamePrefix + ".mobile.gametopic";
        }else{
            key = "gamepublish:" + gamePrefix + ".pc.gametopic";
        }

        List<HomeDynamicGameJsonService.GamePublishTopicDTO> result = objectMapper.readValue(redisTemplate.opsForValue().get(key), new TypeReference<List<GamePublishTopicDTO>>() { });
        List<HomeDynamicGameJsonService.GamePublishTopicDTO> target = new LinkedList<GamePublishTopicDTO>();
        if (null != labels && labels.size() != 0 ) {
            for (String label : labels) {
                for (HomeDynamicGameJsonService.GamePublishTopicDTO data: result){
                    Boolean flag = data.getLabels().contains(label);
                    if (flag) {
                        if (target.size() > 0){
                            Boolean flag2 = false;
                            for (HomeDynamicGameJsonService.GamePublishTopicDTO info: target){
                                //已存在資料集裡，故不再新增
                                flag2 = info.getGameList().equals(data.getGameList());
                            }
                            if (!flag2){
                                target.add(data);
                            }
                        }else{
                            target.add(data);
                        }
                    }
                }
            }
        }
        return target;
    }

    @Data
    @EqualsAndHashCode(of = {"id"})
    public static class GamePublishTopicDTO {

        private String id;
        private String titleIcon;
        private List<String> labels;
        /**
         * 遊戲類別多語系
         */
        private List<MultiLanguageDTO> multiLanguages;
        private List<GameJsonVO> gameList;
    }

    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public static class MultiLanguageDTO {

        @JsonProperty("language")
        private String language;

        @JsonProperty("name")
        private String name;

    }
```

