---

title: "Geospatial encoding"
teaching: 15
exercises: 15
questions:
- "What are the common ways to encode vector geospatial data in Python, and how much is borrowed from broader encoding standards?"
objectives:
- Learn abut common community standards (broader than Python) for vector data encoding, and how they're implemented in core Python libraries.
- Learn to transform from one encoding type to another. Learn the `GeoJSON` format and exchange encoding storage, including the `__geo_interface__` method implemented across libraries.
keypoints:
- Python vector packages implement community standards for vector encoding. While these can seem complex, tools exist for conversion into various forms, and many of the tools including common interfaces for exchanging data across tools.
- Tools exist for easily reading data from file-based and other serialized data formats.

---
