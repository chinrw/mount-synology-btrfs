## Original synology document to mount btrfs
https://kb.synology.com/en-global/DSM/tutorial/How_can_I_recover_data_from_my_DiskStation_using_a_PC

## Cannot mount synology btrfs
BTRFS critical (device dm-1): corrupt leaf: root=1 block=17760886423552 slot=98, invalid root flags, have 0x200000000 expect mask 0x1000000000001
BTRFS error (device dm-1): block=17760886423552 read time tree block corruption detected
BTRFS critical (device dm-1): corrupt leaf: root=1 block=17760886423552 slot=98, invalid root flags, have 0x200000000 expect mask 0x1000000000001
BTRFS error (device dm-1): block=17760886423552 read time tree block corruption detected


## Solution
Your original problem was not (only) a corrupted volume it is that the Synology implementation of btrfs uses flags (1ULL << 32), (1ULL << 33), and (1ULL << 34). To avoid the "invalid root flags" you have to use a kernel which DOES NOT include this change:
https://github.com/torvalds/linux/commit/259ee7754b6793af8bdd77f9ca818bc41cfe9541

Which is approximately mid 4.4 < ver < mid 4.15 . The minimum version is because you also need the v2 cache stuff or volumes will only mount ro.

From: https://www.reddit.com/r/btrfs/comments/kntg3e/comment/i5f8u45/?utm_source=share&utm_medium=web2x&context=3

## Prepare kernel
The latest kernel packages that work with the KB instructions below. Since KB specifically mentions Bionic I think I will stick with that + kernel 4.15.0-108 which seems to be working.

Xenial:
linux-image-4.15.0-107-generic ( 108 not in repo )

- UBUNTU: Ubuntu-4.15.0-109.110
- btrfs: tree-checker: Add ROOT_ITEM check
- UBUNTU: Ubuntu-4.15.0-108.109

Bionic:
linux-image-4.15.0-108-generic

- UBUNTU: Ubuntu-4.15.0-109.110
- btrfs: tree-checker: Add ROOT_ITEM check
- UBUNTU: Ubuntu-4.15.0-108.109

Earliest versions with the free-space fixes are:Ubuntu-4.14.0-11.13( I think 4.13 has them too but can't find the source branch )

From :https://www.reddit.com/r/synology/comments/u6y5qm/comment/i5lk4jn/?utm_source=share&utm_medium=web2x&context=3
