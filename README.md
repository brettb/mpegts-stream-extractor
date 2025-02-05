# MPEG-TS Stream Extractor

Extracts video and KLV streams simultaneously from a MPEG-TS a UDP stream.

# Dependencies
- klvdata
- Python 3.x
- ffmpeg

# Installation

1. Clone this repository:
```bash
git clone https://github.com/limse10/mpegts-stream-extractor.git
cd mpegts-stream-extractor
```

2. Install the required dependencies:
```bash
pip install -r requirements.txt
```

Note: If you encounter issues with the klvdata package, you may need to set your PYTHONPATH:
```bash
export PYTHONPATH=/path/to/klvdata:$PYTHONPATH
```

# Usage

## Configuration

Before starting, ensure your MPEG-TS stream is properly configured. The tool expects:
- Video stream on the first PID (typically 0x100)
- KLV metadata on the second PID (typically 0x101)

## Streaming Video and KLV Data

1. Start the UDP stream:
```bash
ffmpeg -re -i input_video.ts -c copy -f mpegts udp://localhost:1234
```

2. Extract video stream:
```bash
ffmpeg -i udp://localhost:1234 -map 0:0 -f mpegts -codec copy video.ts
```

3. Extract KLV metadata stream:
```bash
ffmpeg -i udp://localhost:1234 -map 0:1 -f data -codec copy stream.klv
```

## Parsing KLV Data

To parse the extracted KLV data stream into human-readable format:
```bash
python parse.py < stream.klv > stream.txt
```

The parsed output will contain metadata such as:
- Timestamp
- UAS Datalink version
- Platform angles (heading, pitch, roll)
- Sensor information (coordinates, altitude, field of view)

## Running Live

For live streaming and parsing, you can use the stream.py script:
```bash
python stream.py [options]
```

Options:
- `--port PORT`: UDP port to listen on (default: 1234)
- `--output-dir DIR`: Directory to save output files (default: current directory)
- `--video-format FORMAT`: Output video format (default: ts)
- `--parse-klv`: Enable real-time KLV parsing

Example:
```bash
python stream.py --port 5000 --output-dir /data/streams --parse-klv
```

## Advanced Usage

### Custom KLV Parsing

You can extend the KLV parsing functionality by creating custom parsers:

```python
from klvdata import parser

class CustomParser(parser.Parser):
    def parse_element(self, key, length, value):
        # Custom parsing logic
        pass
```

### Batch Processing

For processing multiple files:
```bash
python batch_process.py --input-dir /path/to/files --pattern "*.ts"
```

## Troubleshooting

### Common Issues

1. **No KLV data received**
   - Verify the MPEG-TS stream contains KLV metadata
   - Check PID mappings in the stream
   - Ensure proper network connectivity

2. **FFmpeg errors**
   - Verify FFmpeg installation and version
   - Check input stream format
   - Ensure proper codec support

3. **Python path issues**
   - Follow the PYTHONPATH setup in Installation section
   - Verify package installations

### Debug Mode

Enable debug logging for more detailed output:
```bash
python stream.py --debug
```

Please see the KLVdata python package repository for additionl information https://github.com/paretech/klvdata