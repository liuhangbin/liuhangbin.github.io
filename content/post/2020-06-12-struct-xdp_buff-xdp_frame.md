---
title: struct xdp_buff xdp_frame
date: 2020-06-12
---

## struct xdp_buff

```
include/net/xdp.h
struct xdp_mem_info {
	u32 type; /* enum xdp_mem_type, but known size type */
	u32 id;
};

struct xdp_rxq_info {
	struct net_device *dev;
	u32 queue_index;
	u32 reg_state;
	struct xdp_mem_info mem;
} ____cacheline_aligned; /* perf critical, avoid false-sharing */

struct xdp_txq_info {
	struct net_device *dev;
};

struct xdp_buff {
	void *data;
	void *data_end;
	void *data_meta;
	void *data_hard_start;
	struct xdp_rxq_info *rxq;
	struct xdp_txq_info *txq;
	u32 frame_sz; /* frame size to deduce data_hard_end/reserved tailroom*/
};

#define xdp_data_hard_end(xdp)                          \
        ((xdp)->data_hard_start + (xdp)->frame_sz -     \
         SKB_DATA_ALIGN(sizeof(struct skb_shared_info)))

|-------|        <-  data_hard_start
|       |
|-------|        <-  data_meta
|       |
|-------|        <-  data
|       |
|       |
|       |
|-------|        <-  data_end
|       |
|-------|        <-  data_hard_end

```

## struct xdp_frame
```
struct xdp_frame {
	void *data;
	u16 len;
	u16 headroom;
	u32 metasize:8;
	u32 frame_sz:24;
	/* Lifetime of xdp_rxq_info is limited to NAPI/enqueue time,
	 * while mem info is valid on remote CPU.
	 */
	struct xdp_mem_info mem;
	struct net_device *dev_rx; /* used by cpumap */
};

data = xdp->data;
len  = xdp->data_end - xdp->data;
headroom = xdp->data - xdp->data_hard_start - sizeof(*xdp_frame);
metasize = xdp->data - xdp->data_meta;
frame_sz = xdp->frame_sz;

|-------|        <-  (*xdp_frame starts)
|       |        <-  struct xdp_frame {} info stores here
|-------|        <-  meta data
|       |
|-------|        <-  data
|       |
|       |
|       |
|       |
|       |
|-------|
```
