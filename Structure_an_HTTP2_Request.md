
# API传输协议举例(HTTP2)

本传输协议基于HTTP2.0, 兼容Alexa Voice Service v20160207(简称AVS)，为了更好理解HTTP2传输协议的建立、管理，请参考：
- [Managing an HTTP/2 Connection](https://developer.amazon.com/docs/alexa-voice-service/manage-http2-connection.html)
- [Structure an HTTP/2 Request](https://developer.amazon.com/zh/docs/alexa-voice-service/structure-http2-request.html#examples)
<br>
*<font color='red'>注意</font>*：传输协议中的authorization格式为Bearer d~APPID~DeviceID~accessToken。DeviceID请不要带有～符号。  

其中APPID为应用ID，在小米应用商店的应用详情页中查询。DeviceID为设备ID，accessToken为用户授权登录后所产生的Oauth token。  
```java
例如
APPID=28823045615179012345
DeviceID=TestUser1
accessToken=V3_pNMJwjtIOLAQma5947UpnyEXwVYDfZenTWqm5H1ehEuqCuaZUdVlUwR-2qFJr-YylGzf39sfBiwBTsWGrLeJyvEXUpVUnjPSG-RrX1Ge8jYjEjmLhy6vPxpz0C8K6nXX
则authorization=Bearer d~28823045615179012345~TestUser1~V3_pNMJwjtIOLAQma5947UpnyEXwVYDfZenTWqm5H1ehEuqCuaZUdVlUwR-2qFJr-YylGzf39sfBiwBTsWGrLeJyvEXUpVUnjPSG-RrX1Ge8jYjEjmLhy6vPxpz0C8K6nXX    
```

以下举例是为了帮助您快速理解AIVS的HTTP2传输协议。

# 举例
## 举例1:
用户对小爱同学说“播放三国演义”时，相对应的HTTP2请求和返回结果分别为：
#### Request请求


```json
:method = POST
:scheme = https
:path = /v20160207/events
authorization = Bearer d~APPID~DeviceID~accessToken
content-type = multipart/form-data; boundary=this-is-a-boundary

--this-is-a-boundary
Content-Disposition: form-data; name="metadata"
Content-Type: application/json; charset=UTF-8

{
	"event": {
		"header": {
			"namespace": "SpeechRecognizer",
			"name": "Recognize",
			"messageId": "f70ff84e-f30a-4857-bc71-a6c4d612b1fc",
			"dialogRequestId": "f7cb2fe0-7f00-42ca-bc7b-fc21813c0647"
		},
		"payload": {
			"profile": "CLOSE_TALK",
			"format": "AUDIO_L16_RATE_16000_CHANNELS_1"
		}
	},
	"context": [{
		"header": {
			"namespace": "AudioPlayer",
			"name": "PlaybackState"
		},
		"payload": {
			"token": "baisi_28076031",
			"offsetInMilliseconds": 16308,
			"playerActivity": "FINISHED"
		}
	}, {
		"header": {
			"namespace": "SpeechSynthesizer",
			"name": "SpeechState"
		},
		"payload": {
			"token": "822f81d6-3632-48b9-8871-6a77adb63099",
			"offsetInMilliseconds": 0,
			"playerActivity": "FINISHED"
		}
	}, {
		"header": {
			"namespace": "Alerts",
			"name": "AlertsState"
		},
		"payload": {
			"allAlerts": [],
			"activeAlerts": []
		}
	}, {
		"header": {
			"namespace": "Speaker",
			"name": "VolumeState"
		},
		"payload": {
			"volume": 50,
			"muted": false
		}
	}]
}

--this-is-a-boundary
Content-Disposition: form-data; name="audio"
Content-Type: application/octet-stream

{{binary audio attachment}}
--this-is-a-boundary--
```


#### Response返回
**注意**：返回的directive包含两个部分，第一个部分为AudioPlayer，用于播放音频资源。第二个部分为TemplateRuntime，用于显示该资源相关信息。
```json
:status = 200
content-type = multipart/related; boundary=this-is-a-boundary; type="application/json"

--this-is-a-boundary
Content-Type: application/json; charset=UTF-8

{
	"directive": {
		"header": {
			"namespace": "AudioPlayer",
			"name": "Play",
			"messageId": "f70ff84e-f30a-4857-bc71-a6c4d612b1fc",
			"dialogRequestId": "f7cb2fe0-7f00-42ca-bc7b-fc21813c0647"
		},
		"payload": {
			"playBehavior": "REPLACE_ALL",
			"audioItem": {
				"audioItemId": "qqfm:rd003ADbAb0bOuNq",
				"stream": {
					"url": "http://files.ai.xiaomi.com/aiservice/qqfm/bf8e7da91079a7c971ab8e3a47344866e91662de44cc72117800363133b90311.mp3",
					"offsetInMilliseconds": 0,
					"progressReport": {
						"progressReportDelayInMilliseconds": 15000,
						"progressReportIntervalInMilliseconds": 900000
					},
					"token": "rd003ADbAb0bOuNq"
				}
			}
		}
	}
}

--this-is-a-boundary

Content-Type: application/json; charset=UTF-8
{
	"directive": {
		"header": {
			"namespace": "TemplateRuntime",
			"name": "RenderPlayerInfo",
			"messageId": "f70ff84e-f30a-4857-bc71-a6c4d612b1fc",
			"dialogRequestId": "f7cb2fe0-7f00-42ca-bc7b-fc21813c0647"
		},
		"payload": {
			"audioItemId": "qqfm:rd003ADbAb0bOuNq",
			"content": {
				"title": "第001回合",
				"header": "三国演义",
				"mediaLengthInMilliseconds": 0,
				"art": {
					"sources": [{
						"url": "http://imgcache.qq.com/fm/photo/album/rmid_album_360/H/W/0046ZrNH32e4HW.jpg?time=1511154355",
						"widthPixels": 0,
						"heightPixels": 0
					}]
				},
				"provider": {
					"name": "qqfm"
				}
			},
			"controls": [{
				"type": "BUTTON",
				"name": "NEXT",
				"enabled": true,
				"selected": false
			}, {
				"type": "BUTTON",
				"name": "PREVIOUS",
				"enabled": true,
				"selected": false
			}, {
				"type": "BUTTON",
				"name": "PLAY_PAUSE",
				"enabled": true,
				"selected": false
			}]
		}
	}
}
--this-is-a-boundary--
```

