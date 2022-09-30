# Pixel-Local Reconstruction

```mermaid
flowchart TD
	classDef Legacy fill:#ffffff,stroke:#fc1702,stroke-width:4px,color:#fc1702;
	classDef SoA fill:#0000ff,stroke:none,color:#ffffff;
	linkStyle default stroke-width:4px,fill:none,stroke:orange;

	subgraph <strong>GPU workflow</strong>
		rd_g[Raw Data] --> d[Digis]
		d --> c[Clusters]
		c --> h_soa["Hits (SoA)"]
		h_soa --> db[Doublets]
		db --> nt[n-tuplets]
		nt --> pt[Pixel Tracks]
		pt --> pv[Pixel Vertices]
	end 
	
	class rd_g Legacy;
	class d,c,h_soa,db,nt,pt,pv SoA;
	
	subgraph <strong>Products</strong>
		direction TB
		rd_p[Raw Data] --> d_psoa["Digis (Partial SoA)"]
		d_psoa --> h_psoa["Hits (Partial SoA)"]
		h_psoa --> dc_l["Digis & Clusters (Legacy)"] --> h_l["Hits (Legacy)"]
		h_l --> pt_soa_prod["Pixel Tracks (SoA)"]
		pt_soa_prod --> pv_soa_prod["Pixel Vertices (SoA)"]
		pv_soa_prod --> pt_l["Pixel Tracks (Legacy)"]
		pt_l --> pv_l["Pixel Vertices (Legacy)"]
	end
	
	linkStyle 7,8,9,10,11,12,13,14 stroke-width:0px,fill:none,stroke:none;	
	
	rd_p --> rd_g
	d --> d_psoa
	h_soa --> h_psoa
	pt --> pt_soa_prod
	pv --> pv_soa_prod
	linkStyle 15,16,17,18,19 stroke-width:4px,fill:none,stroke:green;

	class rd_p,dc_l,h_l,pt_l,pv_l Legacy;
	class d_psoa,h_psoa,pt_soa_prod,pv_soa_prod SoA;
	
	subgraph <strong>CPU workflow</strong>
		rd_c[Raw Data] -->	d_l["Digis (Legacy)"]
		d_l --> c_l["Clusters (Legacy)"]
		c_l --> h_soa_c["Hits (SoA)"]
		h_soa_c --> db_c[Doublets]
		db_c --> nt_c[n-tuplets]
		nt_c --> pt_soa["Pixel Tracks (SoA)"]
		pt_soa --> pv_soa["Pixel Vertices (SoA)"]
	end
	
	class rd_c,d_l,c_l Legacy;
	class h_soa_c,db_c,nt_c,pt_soa,pv_soa SoA;
	
	h_soa_c --> h_l
	pt_soa --> pt_l
	pv_soa --> pv_l
	linkStyle 27,28,29 stroke-width:4px,fill:none,stroke:red;	
```
