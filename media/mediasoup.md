Worker: pin to core
Router: 1 Worker -- N Router, media swith
    媒体协商，共享codec set，跨Router的只有交集的codec的媒体流才会传输，其余的需要进行transcoding
e.g. codec capabilities for Router
```ts
/**
 * Provides information on the capabilities of a codec within the RTP
 * capabilities. The list of media codecs supported by mediasoup and their
 * settings is defined in the supportedRtpCapabilities.ts file.
 *
 * Exactly one RtpCodecCapability will be present for each supported combination
 * of parameters that requires a distinct value of preferredPayloadType. For
 * example:
 *
 * - Multiple H264 codecs, each with their own distinct 'packetization-mode' and
 *   'profile-level-id' values.
 * - Multiple VP9 codecs, each with their own distinct 'profile-id' value.
 *
 * RtpCodecCapability entries in the mediaCodecs array of RouterOptions do not
 * require preferredPayloadType field (if unset, mediasoup will choose a random
 * one). If given, make sure it's in the 96-127 range.
 */
export type RtpCodecCapability = {
	/**
	 * Media kind.
	 */
	kind: MediaKind;

	/**
	 * The codec MIME media type/subtype (e.g. 'audio/opus', 'video/VP8').
	 */
	mimeType: string;

	/**
	 * The preferred RTP payload type.
	 *
	 * NOTE: Despite it's a mandatory field, it's optional in `mediaCodecs` of
	 * RouterOptions.
	 */
	preferredPayloadType: number;

	/**
	 * Codec clock rate expressed in Hertz.
	 */
	clockRate: number;

	/**
	 * The number of channels supported (e.g. two for stereo). Just for audio.
	 * Default 1.
	 */
	channels?: number;

	/**
	 * Codec specific parameters. Some parameters (such as 'packetization-mode'
	 * and 'profile-level-id' in H264 or 'profile-id' in VP9) are critical for
	 * codec matching.
	 */
	parameters?: Record<string, unknown>;

	/**
	 * Transport layer and codec-specific feedback messages for this codec.
	 */
	rtcpFeedback?: RtcpFeedback[];
};
```

Transport: 1 Router -- N Tranports, media channel