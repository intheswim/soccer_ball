//http://forum.java.sun.com/thread.jsp?thread=417056&forum=54&message=1843878
import java.awt.*;
import java.applet.*;
import java.awt.geom.*;
import java.awt.geom.Point2D;
import java.awt.Graphics;
import java.awt.Graphics2D;
import javax.swing.JApplet;
import javax.swing.JComponent;

class DPoint
{
	double x,y,z;
	public DPoint (double x, double y, double z)
		{
		this.x = x; 
		this.y = y;
		this.z = z;
		}
	public DPoint (DPoint other_point)
		{
		x = other_point.x;
		y = other_point.y;
		z = other_point.z;
		}

	public void Copy (DPoint point)
		{
		x = point.x;
		y = point.y;
		z = point.z;
		}
	public void Normalize ()
		{
		double k = 1/Math.sqrt(x*x + y*y + z*z);
		x = x * k;
		y = y * k;
		z = z * k;
		}
}

public class SoccerBall extends java.applet.Applet {
	Image buf;
	float radius;
	int centerX, centerY;
	DPoint[] stored;
	DPoint[] vertex;
	int seams_parts;
	double delta, tilt, delta1, tilt1;
	boolean captured;

	public boolean mouseDown(Event e, int x, int y)
		{
		double x1 = x - centerX;
		double y1 = y - centerY;

		if (Math.sqrt(x1*x1 + y1*y1) < radius)
			{
			delta1 = Math.asin (x1/Math.sqrt(radius*radius-y1*y1));
			tilt1 = Math.asin (y1/Math.sqrt(radius*radius-x1*x1));

			captured = true;
			}
		return true;
		}

	public boolean mouseUp(Event e, int x, int y)
		{
		if (captured)
			{
			captured = false;
			updateStorage (delta, tilt);
			delta = 0;
			tilt = 0;
			}
		return true;
		}

	public boolean mouseDrag(Event e, int x, int y)
		{
		double x1 = x - centerX;
		double y1 = y - centerY;

		if (captured && Math.sqrt(x1*x1 + y1*y1) < radius)
			{
			double delta2 = Math.asin (x1/Math.sqrt (radius*radius-y1*y1));
			double  tilt2 = Math.asin(y1/Math.sqrt(radius*radius-x1*x1));

			delta = (delta2 - delta1); 
			tilt = (tilt2 - tilt1); 

			updateCurrent (delta, tilt);

			// repaint buffer image
			drawAll (buf.getGraphics());
			// don't call repaint: it calls update, which first repaints background
			// and this causes flicker
			//repaint();
			// call paint() directly instead!
			paint(getGraphics());
			}
		return true;
		}

	protected void setPenWidth(Graphics2D g2d, float width) 
		{
		//g2d.setStroke(new BasicStroke((double)width));
		g2d.setStroke(new BasicStroke(width, BasicStroke.CAP_SQUARE, 
				     BasicStroke.JOIN_MITER, 10.0f, null, 0.0f));

		}

	protected void fillCircle (Graphics2D g2d, int cx, int cy, float radius) 
		{
		Ellipse2D circle = new Ellipse2D.Float(cx-radius, cy-radius, 
					2*radius, 2*radius);
		g2d.fill(circle);
		}

	protected void drawCircle (Graphics2D g2d, int cx, int cy, float radius) 
		{
		Ellipse2D circle = new Ellipse2D.Float(cx-radius, cy-radius, 
					2*radius, 2*radius);
		g2d.draw(circle);
		}

	void TransformDelta (DPoint point, double delta)
		{
		double x = point.x;
		double z = (point.z) ;

		point.x = x * Math.cos(delta) + z * Math.sin(delta) ;
		point.z = z * Math.cos(delta) - x * Math.sin(delta) ;
		}

	void TransformTilt (DPoint point, double tilt)
		{
		double y = point.y ;
		double z = point.z ;

		point.y = y * Math.cos (tilt) + z * Math.sin(tilt) ;
		point.z = z * Math.cos (tilt) - y * Math.sin(tilt) ;
		}

	double GetZCoord (DPoint pt)
		{
		return pt.z;
		}

	double DistanceDPoints (DPoint pt1, DPoint pt2)
		{
		return	(pt1.x - pt2.x) * (pt1.x - pt2.x) +
				(pt1.y - pt2.y) * (pt1.y - pt2.y) +
				(pt1.z - pt2.z) * (pt1.z - pt2.z) ;
		}

	double AngleDPoints (DPoint point1, DPoint point2)
		{
		double d=(point1.x * point2.x)  +
				 (point1.y * point2.y)  +
				 (point1.z * point2.z) ;

		return Math.asin(d);
		}

	void Transform (int x, int y, DPoint pt)
		{
		x = (int)(centerX + radius * pt.x) ;
		y = (int)(centerY + radius * pt.y) ;
		}

	void updateStorage (double delta, double tilt)
		{
		for (int i = 0; i < 12; i++)
			{
			TransformDelta (stored[i], delta);
			TransformTilt (stored[i], tilt);
			}
		}
	void updateCurrent (double delta, double tilt)
		{
		for (int i = 0; i < 12; i++)
			{
			vertex[i].Copy(stored[i]);
			TransformDelta (vertex[i], delta);
			TransformTilt (vertex[i], tilt);
			}
		}

	void initVertices ()
		{
		double alpha;
		double beta = Math.atan (2.0);

		int i;

		vertex [0].x = 0;
		vertex [0].y = 1;
		vertex [0].z = 0;

		for (i=1; i <= 5; i++)
			{
			alpha = 0.4*java.lang.Math.PI*(i-1); 
			vertex[i].x = Math.sin(beta)*Math.cos(alpha);
			vertex[i].y = Math.cos(beta);
			vertex[i].z = Math.sin(beta)*Math.sin(alpha);
			}

		for (i=6; i<12; i++)
			{
			vertex[i].x = -vertex[i-6].x;
			vertex[i].y = -vertex[i-6].y;
			vertex[i].z = -vertex[i-6].z;
			}	
		for (i=0; i<12; i++)
			{
			stored[i].Copy(vertex[i]);
			}
		}


	void DrawLine (DPoint pt1, DPoint pt2, Graphics2D g2d)
		{
		double x1 = centerX + radius * pt1.x ;
		double y1 = centerY + radius * pt1.y ;
		double z1 = radius * pt1.z ;

		double x2 = centerX + radius * pt2.x ;
		double y2 = centerY + radius * pt2.y ;
		double z2 = radius * pt1.z ;

		if (z1 < 0 && z2 < 0)
			return;

		g2d.drawLine ((int)x1, (int)y1, (int)x2, (int)y2);	
		}

	void FindIntermediatePoint (DPoint pt1, DPoint pt2, DPoint out, double fraction)
		{
		out.x = pt1.x + (pt2.x - pt1.x) * fraction;
		out.y = pt1.y + (pt2.y - pt1.y) * fraction;
		out.z = pt1.z + (pt2.z - pt1.z) * fraction;

		out.Normalize();
		}

	int findFiveClosest (DPoint from, int start, DPoint[] points, int cnt, double min)
		{
		double dist=0;

		for (int i = start; i < cnt; i++)
			{
			dist = 
			(from.x-points[i].x) * (from.x-points[i].x) +
			(from.y-points[i].y) * (from.y-points[i].y) +
			(from.z-points[i].z) * (from.z-points[i].z) ;

			if (dist < min)	return i;
			}
		return -1;
		}

	void drawPaintedPentagons (DPoint[] pentagon_points, Graphics2D g2d)
		{
		DPoint[] pent_set1 = new DPoint [5];
		int i, j, cnt, index, closest, total_parts = 5 * seams_parts;
		double k;
		DPoint temp;
		DPoint[] polyline = new DPoint[total_parts];
		DPoint[] edge = new DPoint[2];
		double x,y,z;

		for (i=0; i < 12; i++)
			{
			closest = 0;
			cnt=0;
			while (0 <= (closest = findFiveClosest (vertex[i], closest, pentagon_points, 60, 0.4)))
				{
				//DrawLine (&points[i], &pentagon_points[closest], hdc, tilt);
				pent_set1[cnt] = pentagon_points[closest]; 
				closest++;
				cnt++;
				}
			// safety net
			if (cnt < 5) return;

			for (cnt=0; cnt<4;cnt++)
				{
				for (j = cnt+1; j<5; )
					{
					if (DistanceDPoints(pent_set1[cnt], pent_set1[j]) > 0.4) j++;					
					else 
						{
						if (j != cnt+1)
							{
							temp = pent_set1[cnt+1];
							pent_set1[cnt+1] = pent_set1[j];
							pent_set1[j] = temp;
							}
						break;
						}
					}
				} // end of for()

			index = 0;
			for (cnt=0; cnt < 5 ;cnt++)
				{
				for (j = 0; j < seams_parts; j++)
					{
					polyline[index] = new DPoint (0, 0, 0);
					FindIntermediatePoint (pent_set1[cnt], pent_set1[(cnt+1)%5], polyline[index], ((double)j)/seams_parts);
					index++;
					}
				}

			cnt=0;
			int positive_start=-1;

			for (index=0;index < total_parts; index++)
				{
				if (GetZCoord (polyline[index]) >= 0) 
					{
					cnt++;
					}
				}

			if (cnt < total_parts && cnt > 0) // not all points are visible
				{
				double z1, z2;

				for (index = 0; index < total_parts; index++)
					{
					if (GetZCoord (polyline[index])<0 && GetZCoord (polyline[(index+1)%total_parts])>=0)
						{
						positive_start = (index+1) % total_parts;
						z1 = GetZCoord (polyline[index]);
						z2 = GetZCoord (polyline[(index+1)%total_parts]);

						edge[0] = new DPoint (
							polyline[index].x + ((-z1)/(z2-z1))*(polyline[(index+1)%total_parts].x - polyline[index].x),
							polyline[index].y +	((-z1)/(z2-z1))*(polyline[(index+1)%total_parts].y - polyline[index].y),
							polyline[index].z + ((-z1)/(z2-z1))*(polyline[(index+1)%total_parts].z - polyline[index].z));
						}

					if (GetZCoord (polyline[index])<0 && GetZCoord (polyline[(index+total_parts-1)%total_parts])>=0)
						{
						z1 = GetZCoord (polyline[index]);
						z2 = GetZCoord (polyline[(index+total_parts-1)%total_parts]);

						edge[1] = new DPoint (
							polyline[index].x + ((-z1)/(z2-z1))*(polyline[(index+total_parts-1)%total_parts].x - polyline[index].x),
							polyline[index].y +	((-z1)/(z2-z1))*(polyline[(index+total_parts-1)%total_parts].y - polyline[index].y),
							polyline[index].z + ((-z1)/(z2-z1))*(polyline[(index+total_parts-1)%total_parts].z - polyline[index].z));
						}
					}
				index = 0;
				int new_set_cnt = 0;
				index = positive_start;

				double angle = AngleDPoints (edge[0], edge[1]);
				int extra_points = 1 + (int)(angle*180/Math.PI)/2;

				DPoint[] new_set = new DPoint[total_parts + extra_points + 2];

				new_set[0] = new DPoint(edge[0].x, edge[0].y, edge[0].z);

				while (new_set_cnt < cnt)
					{
					if (GetZCoord (polyline[index])>=0)
						{
						new_set[new_set_cnt+1] = new DPoint (polyline[index].x,
									polyline[index].y, polyline[index].z);
						new_set_cnt++;
						}
					index++;
					if (index >= total_parts) 
						index=0;
					}

				new_set[new_set_cnt+1] = new DPoint (edge[1].x, edge[1].y, edge[1].z);
				new_set_cnt++;

				for (index=1; index < extra_points; index++)
					{
					new_set[new_set_cnt+1] = new DPoint (0,0,0);
					FindIntermediatePoint (edge[1], edge[0], new_set[new_set_cnt+1], ((double)index)/extra_points);
					new_set_cnt++;
					} 

				GeneralPath polygon = new GeneralPath ();
				for (index = 0; index < new_set_cnt+1; index++)
					{
					if (index==0)
						polygon.moveTo((float)(centerX + radius * new_set[index].x), 
									 (float)(centerY + radius * new_set[index].y));
					else
						polygon.lineTo((float)(centerX + radius * new_set[index].x), 
									 (float)(centerY + radius * new_set[index].y));
					}
				polygon.closePath();
				g2d.fill(polygon);
				}
			else if (cnt == total_parts)
				{
				GeneralPath polygon = new GeneralPath ();
				for (index = 0; index < total_parts; index++)
					{
					if (index==0)
						polygon.moveTo((float)(centerX + radius * polyline[index].x), 
									 (float)(centerY + radius * polyline[index].y));
					else
						polygon.lineTo((float)(centerX + radius * polyline[index].x), 
									 (float)(centerY + radius * polyline[index].y));
					}
				polygon.closePath();
				g2d.fill(polygon);
				}	
			}
		}

	void drawSeams (DPoint[] pentagon_points, Graphics2D g2d)
		{
		int closest, i, j, cnt, index;
		float x,y;
		DPoint[] line = new DPoint[seams_parts + 1];
		DPoint[] series1 = new DPoint[seams_parts + 1];
		DPoint[] series2 = new DPoint[seams_parts + 1];
		DPoint[] common = new DPoint [(seams_parts + 1) * 2];
		DPoint cross1, cross2, vector;

		cross1 = new DPoint(0,0,0);
		cross2 = new DPoint(0,0,0);

		for (i=0; i < 60; i++)
			{
			closest = i;
			while (0 <= (closest = findFiveClosest (pentagon_points[i], closest+1, pentagon_points, 60, 0.4)))
				{
				line[0] = new DPoint (pentagon_points[i].x, pentagon_points[i].y,
										pentagon_points[i].z);

				line[seams_parts] = new DPoint (pentagon_points[closest].x, pentagon_points[closest].y,
										pentagon_points[closest].z);

				vector = new DPoint (pentagon_points[i].y * pentagon_points[closest].z -
								     pentagon_points[closest].y * pentagon_points[i].z,

									 pentagon_points[i].z * pentagon_points[closest].x -
									 pentagon_points[closest].z * pentagon_points[i].x,

									 pentagon_points[i].x * pentagon_points[closest].y -
									 pentagon_points[closest].x * pentagon_points[i].y);

				vector.Normalize();
				vector.x = vector.x / 100;
				vector.y = vector.y / 100;
				vector.z = vector.z / 100;

				for (j = 1; j <= seams_parts-1; j++)
					{
					line[j] = new DPoint (0, 0, 0);
					FindIntermediatePoint (pentagon_points[i], pentagon_points[closest], line[j], ((double)j)/seams_parts);
					}

				for (j = 0; j <= seams_parts; j++)
					{
					cross1.x = line[j].x + vector.x;
					cross1.y = line[j].y + vector.y;
					cross1.z = line[j].z + vector.z;

					cross2.x = line[j].x - vector.x;
					cross2.y = line[j].y - vector.y;
					cross2.z = line[j].z - vector.z;

					cross1.Normalize ();
					cross2.Normalize ();

					series1[j] = new DPoint(cross1);
					series2[j] = new DPoint(cross2);
					}

				cnt=0;
				for (j=0; j <= seams_parts; j++)
					{
					if (series1[j].z >= 0)
						{
						common [cnt] = series1[j];
						cnt++;
						}
					}

				for (j=seams_parts; j >= 0; j--)
					{
					if (series2[j].z >= 0)
						{
						common[cnt] = series2[j];
						cnt++;
						}
					}

				GeneralPath polygon = new GeneralPath ();
				index=0;

				for (j = 0; j < cnt; j++)
					{
					if (common[j].z > 0)
						{
						x = (float)(centerX + radius * common[j].x);
						y =	(float)(centerY + radius * common[j].y);

						if (index == 0)						
							polygon.moveTo (x,y);							
						else
							polygon.lineTo (x, y);

						index++;
						}
					}
				if (index > 1)
					{
					g2d.fill(polygon);
					}
				}
			}
		}

	public void FindPentagonPoints (DPoint[] pentagon_points, Graphics2D g2d)
		{
		int closest, i;
		int cnt = 0;
		DPoint pent1, pent2;

		pent1 = new DPoint(0,0,0);
		pent2 = new DPoint(0,0,0);
		
		for (i=0; i < 12; i++)
			{
			closest = i;
			while (0 <= (closest = findFiveClosest (vertex[i], closest+1, vertex, 12, 1.2)))
				{
				FindIntermediatePoint (vertex[i], vertex[closest], pent1, 1/3.0);
				FindIntermediatePoint (vertex[i], vertex[closest], pent2, 2/3.0);

				if (cnt <= 58)
					{
					pentagon_points[cnt].x = pent1.x;
					pentagon_points[cnt].y = pent1.y;
					pentagon_points[cnt].z = pent1.z;

					cnt++;
					pentagon_points[cnt].x = pent2.x;
					pentagon_points[cnt].y = pent2.y;
					pentagon_points[cnt].z = pent2.z;

					cnt++;
					}
				} 
			}
		}

	public void init() {
		stored = new DPoint[12];
		vertex = new DPoint[12];
		for (int i=0; i<12; i++)
			{
			stored[i] = new DPoint(0,0,0);
			vertex[i] = new DPoint(0,0,0);
			}
		initVertices ();

		radius = 120;
		seams_parts = (int)(radius/10);		
		delta = 0;
		tilt = 0;
		centerX = getSize().width/2;
		centerY = getSize().height/2;
		buf = createImage (getSize().width, getSize().height);
		drawAll (buf.getGraphics());
		}

	public void paint(Graphics g)
		{	
		g.drawImage (buf,0,0,this);
		}

	public void drawAll (Graphics g) 
		{	
		g.setColor (Color.lightGray);	
		g.fillRect (0, 0, getSize().width, getSize().height);
//		setBackground(Color.cyan);
		Graphics2D g2d = (Graphics2D)g;	
		setPenWidth(g2d, 1); 
		g2d.setPaint(Color.white);
		fillCircle (g2d, centerX, centerY, radius) ;
		DrawIcosahedron (g2d);
		}

	void DrawIcosahedron (Graphics2D g2d)
		{
		int i;
		DPoint[] pentagon_points = new DPoint[60];
		for (i=0; i<60; i++)
			{
			pentagon_points[i] = new DPoint(0,0,0);
			}

	//	setPenWidth(g2d, 1); 
		g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

		FindPentagonPoints (pentagon_points, g2d);		
		g2d.setColor (Color.darkGray);	

		drawPaintedPentagons (pentagon_points, g2d);
		g2d.setColor (Color.black);	

		setPenWidth(g2d, 1.0f); 

		drawSeams (pentagon_points, g2d);

		drawCircle (g2d, centerX, centerY, radius) ;
		}

	}
	
	