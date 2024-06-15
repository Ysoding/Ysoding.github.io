## Res

- [pcap-savefile](https://www.tcpdump.org/manpages/pcap-savefile.5.html)
- [Wiki on SYN flood](https://en.wikipedia.org/wiki/SYN_flood)
- [IPv4 Packet structure](https://en.wikipedia.org/wiki/Internet_Protocol_version_4#Packet_structure)
- [TCP Segment structure](https://en.wikipedia.org/wiki/Transmission_Control_Protocol#TCP_segment_structure)

[Code](https://github.com/Ysoding/cs/blob/main/computer-system/bits_bytes/syn_flood/syn_flood/src/main.rs)

```rust

    // cd syn_flood/ && cargo build && cd .. && cat synflood.pcap | syn_flood/target/debug/syn_flood
    // 95829 packets parsed with 56298 connections, 39531 (70.22%) acknowledged
    for packet in f.packets.iter() {
        // link layer header (4 bytes)
        // network layer header
        // transport layer header
        // application layer data
        let ipv4_packet = ipv4::Packet::from_bytes(&packet.payload[4..]).unwrap();
        let tcp_header = tcp::SegmentHeader::from_bytes(&ipv4_packet.payload).unwrap();
        if tcp_header.is_initiated() {
            initiated += 1.;
        }
        if tcp_header.is_acknowledgment() {
            acknowledged += 1.;
        }
    }
```
