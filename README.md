# movie_publisher

This node serves a video file as video topic source (`sensor_msgs/Image` and friends).

It handles any file formats the system installation of ffmpeg can decode.

In the `immediate` mode, it can also be used to convert video files to bagfiles in a batch.

## movie_publisher_node

### Topics

- `movie` (`sensor_msgs/Image`): The published movie.

### Node-private parameters:

- `movie_file` (string, required): Path to the movie to play. Any format that ffmpeg can decode.
- `fps` (float, optional): If set, the playback will be at the specified FPS (speeding up/slowing down the movie).
- `start` (float|tuple|string, optional): If set, playback will start from the specified time.
      Can be expressed in seconds `(15.35)`, in `(min, sec)`, in `(hour, min, sec)`,
      or as a string: `'01:03:05.35'`.
      Cannot be set together with `end` and `duration`.
- `end` (float|tuple|string, optional): If set, playback will stop at the specified time (not affected by start).
      Can be expressed in seconds `(15.35)`, in `(min, sec)`, in `(hour, min, sec)`,
      or as a string: `'01:03:05.35'`.
      Cannot be set together with `start` and `duration`.
- `duration` (float|tuple|string, optional): If set, playback will have this duration. If end is also set, the
      duration is counted from the end of the clip, otherwise, it is the duration from the start of the clip.
      Can be expressed in seconds `(15.35)`, in `(min, sec)`, in `(hour, min, sec)`,
      or as a string: `'01:03:05.35'`.
      Cannot be set together with `start` and `end`.
- `loop` (bool, default False): Whether to loop the movie until the node is shut down. Exludes `immediate`.
- `immediate` (bool, default False): If True, the movie will be processed and published as quickly as possible not
      waiting for the real time. The timestamps in the resulting messages act "real-world-like" (i.e. 15 FPS means
      the frames' timestamps will be 1/15 sec apart). You can set `fake_time_start` if you want these timestamps to
      begin from a non-zero time. Excludes `loop`.
- `fake_time_start` (float, default 0.0): Used with `immediate` to specify the timestamp of the first message.
- `frame_id` (string, default ""): The frame_id used in the messages' headers.
- `spin_after_end` (bool, default False): If True, a rospy.spin() is called after the movie has been published.
- `verbose` (bool, default False): If True, logs info about every frame played.
- `wait_after_publisher_created` (float, default 1.0): A workaround for the case where you need to give your
      subscribers some time after the publisher was created. Tweak this number until you get no missing start
      messages.
- `ffmpeg` (string, default ""): If nonempty, specifies the (absolute) path to the ffmpeg binary to use.

## movie_publisher.launch

This launch file takes arguments with the same name as the node's parameters.

Additionally, it takes these arguments:

- `transport` (string, default `'raw'`): Type of the image transport. In not `'raw'`, the
      launch file will start up an `image_transport/republish` node that publishes the video
      encoded to this transport type. In ROS Indigo and older, you also need to set 
      `transport_run_republish_node` to `true` for this setting to take effect.
- `transport_run_republish_node` (bool, default `false`): Set to `true` if you change the
      value of `transport`.
- `republished_topic_basename` (string, default `movie_$(arg transport)`): Base name of the
      topic that will serve the republished messages. Full name of the topic will be
      `$(arg republished_topic_basename)/$(arg transport)`. The base name cannot be the same
      as the topic the `movie_publisher_node` subscribes to (`movie` by default).