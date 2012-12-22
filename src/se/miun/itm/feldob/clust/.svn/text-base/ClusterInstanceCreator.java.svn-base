/*-- $Copyright (C) 2012 Felix Dobslaw$


Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is furnished
to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED,
INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A
PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT
HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
 */
package se.miun.itm.feldob.clust;

import java.awt.Color;
import java.awt.Container;
import java.awt.Dimension;
import java.awt.Font;
import java.awt.FontMetrics;
import java.awt.Graphics;
import java.awt.Graphics2D;
import java.awt.Insets;
import java.awt.image.BufferedImage;
import java.io.BufferedWriter;
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.Collection;
import java.util.Collections;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Random;
import java.util.Set;

import javax.imageio.ImageIO;
import javax.swing.JFrame;
import javax.swing.JLabel;

import se.miun.itm.input.InPUTConfig;
import se.miun.itm.input.model.InPUTException;
import se.miun.itm.input.model.design.DesignSpace;
import se.miun.itm.input.model.design.IDesignSpace;

/**
 * A problem instance generator for two dimensional clustering. It randomly
 * creates arbitrarily formed classes of clusters with uniformly distributed
 * data-points of predefined densities. The clusters are formed by printing a
 * predefined amount of randomly chosen characters to an image of a given pixel
 * size. Overlapping characters are merged into cluster spaces, for which
 * clusters are formed in a next step. The cluster assignment for each point is
 * saved in a text file. The generator could for instance be used to test and
 * compare clustering algorithms. The parameters are drawn unified at random
 * from the InPUT configuration file by name "clusterSpace.xml".
 * 
 * @author Felix Dobslaw
 * 
 */
public class ClusterInstanceCreator {

	private static final int[] relativePos = { -1, 0, 1 };

	class ClusterInfo {

		private final Map<Integer, Map<Integer, Integer>> clusters; // x,y,clusterId
		private Map<Integer, Map<Integer, Set<Integer>>> clusterReferences; // clusterId,x,y

		public ClusterInfo(int totalX) {
			clusters = createEmpty(totalX);
			clusterReferences = new HashMap<Integer, Map<Integer, Set<Integer>>>();
		}

		private Map<Integer, Map<Integer, Integer>> createEmpty(int width) {
			Map<Integer, Map<Integer, Integer>> clusters = new HashMap<Integer, Map<Integer, Integer>>();
			for (int i = 0; i < width; i++)
				clusters.put(i, new HashMap<Integer, Integer>());
			return clusters;
		}

		public void addToClusterReferences(Integer chosenCluster, int x, int y) {
			clusters.get(x).put(y, chosenCluster);
			Map<Integer, Set<Integer>> cluster = clusterReferences
					.get(chosenCluster);

			if (cluster == null) {
				cluster = new HashMap<Integer, Set<Integer>>();
				clusterReferences.put(chosenCluster, cluster);
			}

			Set<Integer> xs = cluster.get(x);

			if (xs == null) {
				xs = new HashSet<Integer>();
				cluster.put(x, new HashSet<Integer>());
			}

			cluster.get(x).add(y);
		}

		public Collection<? extends Integer> getClusterIds() {
			return clusterReferences.keySet();
		}

		public void redoIndexing(Map<Integer, Integer> lookup) {

			Map<Integer, Integer> ys;
			for (Integer x : clusters.keySet()) {
				ys = clusters.get(x);
				for (Integer y : clusters.get(x).keySet()) {
					ys.put(y, lookup.get(ys.get(y)));
				}
			}
		}

		public Map<Integer, Map<Integer, Integer>> getClusters() {
			return clusters;
		}

		public Integer getClusterid(int x, int y) {
			return clusters.get(x).get(y);
		}

		public void remove(Integer oldCluster) {
			clusterReferences.remove(oldCluster);
		}

		public Map<Integer, Set<Integer>> getClusterMap(Integer oldCluster) {
			return clusterReferences.get(oldCluster);
		}

		public int getAmountClusters() {
			return clusterReferences.size();
		}

		public int getAmountPointsInCluster(int i) {
			Map<Integer, Set<Integer>> cluster = getClusterMap(i);
			int total = 0;
			for (Integer x : cluster.keySet()) {
				total += cluster.get(x).size();
			}
			return total;
		}

	}

	public ClusterInstanceCreator() throws InPUTException, IOException,
			InterruptedException {
		IDesignSpace config = new DesignSpace("clusterSpace.xml"); // read
																	// config
		File folder = createFolder((String) config.next("Folder")); // where to
																	// store?
		int amountInstances = config.next("AmountInstances"); // how many
																// instances?

		JFrame frame = null;
		Random rng = InPUTConfig.getValue("random");
		System.out.println("\nStarting cluster creation...\n");
		BufferedImage original;
		for (int i = 1; i <= amountInstances; i++) {
			System.out.print("\tcluster " + i + "/" + amountInstances
					+ " ... ");
			frame = initFrame();
			original = createInstance(folder, i, frame, config, rng);
			createClustering(original, folder, i, config);

			frame.dispose();
			System.out.println("done!");
		}
		System.out.println();
		System.out
				.println("All clusters have successfully been created and can be found in folder:\n");
		System.out.println("\t" + folder.getAbsolutePath());
		System.out.println();
	}

	private File createFolder(String folderName) {
		File instanceFolder = new File(folderName);
		if (!instanceFolder.exists())
			instanceFolder.mkdirs();
		return instanceFolder;
	}

	public static void main(String[] args) throws InPUTException,
			InterruptedException, IOException {
		if (args == null || args.length == 0) {
			new ClusterInstanceCreator();
		}else if (args[0].startsWith("-v") || args[0].startsWith("--v")){
			System.out.println("\nClusterInstanceCreator version 0.1 (Requires Java 1.6++)\nauthor: Felix Dobslaw\nopen source under MIT license\n");
		}
	}

	private static JFrame initFrame() {
		JFrame frame = new JFrame();
		frame.setLayout(null);
		frame.getContentPane().setBackground(Color.WHITE);
		frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		return frame;
	}

	private BufferedImage createInstance(File folder, int index, JFrame frame,
			IDesignSpace space, Random rng) throws InPUTException, IOException {

		int size = space.next("FrameSize"); // what is the square size of the
											// image?

		Container pane = preparePane(frame, size);

		int tokens = space.next("AmountTokens"); // how many characters?

		printTokens(frame, space, rng, size, pane, tokens);

		frame.pack();

		return export(size, folder, index, frame);
	}

	private Container preparePane(JFrame frame, int size) {
		Container pane = frame.getContentPane();

		pane.setPreferredSize(new Dimension(size, size));

		pane.setLayout(null);
		return pane;
	}

	private void printTokens(JFrame frame, IDesignSpace space, Random rng,
			int size, Container pane, int tokens) throws InPUTException {
		Insets insets = frame.getInsets();

		int tenth = size / 10;
		int radius = size + tenth;
		Dimension pref;
		JLabel textLabel;
		for (int i = 0; i < tokens; i++) {
			int tokenSize = space.next("TokenSize");
			Font font = new Font("Serif", Font.PLAIN, tokenSize);
			textLabel = new JLabel(randomCharacter(rng));
			textLabel.setFont(font);
			FontMetrics m = textLabel.getFontMetrics(font);
			pane.add(textLabel);
			pref = textLabel.getPreferredSize();
			textLabel.setBounds(
					insets.left - tenth + rng.nextInt(radius),
					(insets.top - m.getHeight() / 6) - tenth
							+ rng.nextInt(radius), pref.width, pref.height);
		}
	}

	private static void export(BufferedImage biNew, File folder, int index) {
		Graphics g = biNew.createGraphics();
		g.dispose();
		try {
			ImageIO.write(biNew, "png", new File(folder.getAbsolutePath()
					+ File.separator + index + "_cluster.png"));
		} catch (Exception e) {
		}
	}

	private void createClustering(BufferedImage bi, File folder, int index,
			IDesignSpace space) throws IOException, InPUTException {

		ClusterInfo clusters = initClusters(bi);

		BufferedImage newBi = new BufferedImage(bi.getWidth(), bi.getHeight(),
				BufferedImage.TYPE_INT_ARGB);

		Graphics2D graphics = newBi.createGraphics();

		graphics.setPaint(Color.WHITE);
		graphics.fillRect(0, 0, bi.getWidth(), bi.getHeight());

		StringBuilder b = new StringBuilder();
		b.append("X Y Cluster\n");

		int points, amountEntriesToBeDrawn;
		double clusterDensity;
		List<Integer> entriesToBeDrawn;
		for (Integer i : clusters.getClusterIds()) {
			clusterDensity = space.next("ClusterDensity");
			points = clusters.getAmountPointsInCluster(i);
			amountEntriesToBeDrawn = (int) (points * clusterDensity);
			entriesToBeDrawn = decideEntriesToBeDrawn(points,
					amountEntriesToBeDrawn);
			Collections.sort(entriesToBeDrawn);
			draw(folder, index, entriesToBeDrawn, clusters, i, newBi, bi, b);
		}

		BufferedWriter writer = new BufferedWriter(new FileWriter(
				folder.getAbsolutePath() + File.separator + index + ".txt"));
		writer.append(b);
		writer.close();

		export(newBi, folder, index);
	}

	private static String randomCharacter(Random rng) {
		Random r = new Random();
		int offset = r.nextInt(26);
		char c = rng.nextBoolean() ? 'a' : 'A';
		return "" + (char) (offset + c);
	}

	private static BufferedImage export(int size, File folder, int index,
			JFrame frame) {

		BufferedImage bi = new BufferedImage(size, size,
				BufferedImage.TYPE_INT_RGB);
		Graphics2D g2d = bi.createGraphics();
		frame.getContentPane().print(g2d);
		g2d.dispose();
		try {
			ImageIO.write(bi, "png", new File(folder.getAbsolutePath()
					+ File.separator + index + ".png"));
		} catch (Exception e) {
		}
		return bi;
	}

	private void draw(File folder, int index, List<Integer> entriesToBeDrawn,
			ClusterInfo clusters, int clusterId, BufferedImage newBi,
			BufferedImage bi, StringBuilder b) throws IOException {

		Map<Integer, Set<Integer>> cluster = clusters.getClusterMap(clusterId);

		int counter = 0;
		int entriesCounter = 0;
		for (Integer x : cluster.keySet()) {
			for (Integer y : cluster.get(x)) {
				if (entriesToBeDrawn.get(entriesCounter) == counter) {
					if (bi.getRGB(x, y) != Color.white.getRGB()) {
						newBi.setRGB(x, y, Color.black.getRGB());
					}
					entriesCounter++;
					if (entriesToBeDrawn.size() <= entriesCounter)
						return;
					b.append(x + " " + y + " " + clusters.getClusterid(x, y));
					b.append('\n');
				}
				counter++;
			}
		}
	}

	private List<Integer> decideEntriesToBeDrawn(int points,
			int amountEntriesToBeDrawn) {
		List<Integer> l = new ArrayList<Integer>(points);
		for (int i = 1; i <= points; i++) {
			l.add(i);
		}
		Collections.shuffle(l);
		return l.subList(0, amountEntriesToBeDrawn);
	}

	private ClusterInfo initClusters(BufferedImage bi) {
		ClusterInfo clusters = new ClusterInfo(bi.getWidth());

		int clusterId = 0;
		for (int y = 0; y < bi.getHeight(); y++) {
			for (int x = 0; x < bi.getWidth(); x++) {
				clusterId = createCluster(bi, clusters, clusterId, y, x);
			}
		}
		organizeClusterIds(clusters);
		return clusters;
	}

	private static void organizeClusterIds(ClusterInfo clusters) {

		List<Integer> clusterIds = new ArrayList<Integer>(
				clusters.getClusterIds());
		Collections.sort(clusterIds);

		Map<Integer, Integer> lookup = new HashMap<Integer, Integer>();

		for (int i = 1; i <= clusterIds.size(); i++)
			lookup.put(clusterIds.get(i - 1), i);

		clusters.redoIndexing(lookup);
	}

	private static int createCluster(BufferedImage bi, ClusterInfo clusters,
			int clusterId, int y, int x) {
		int color;
		Integer chosenCluster;
		color = bi.getRGB(x, y);
		if (color != Color.WHITE.getRGB()) {
			if (clusters.getClusterid(x, y) == null) {
				// is black
				chosenCluster = getExistingCluster(bi, clusters, x, y);
				if (chosenCluster == null) {
					clusterId++;
					chosenCluster = clusterId;
				}
				clusters.addToClusterReferences(chosenCluster, x, y);
			}
		}
		return clusterId;
	}

	private static Integer getExistingCluster(BufferedImage bi,
			ClusterInfo clusters, int x, int y) {
		Integer chosenCluster = null;

		Set<Integer> neighborClusters = new HashSet<Integer>();

		if (x - 1 >= 0 && bi.getRGB(x - 1, y) != Color.WHITE.getRGB()) {
			chosenCluster = clusters.getClusterid(x - 1, y);
			neighborClusters.add(chosenCluster);
		}

		int xpos;
		for (int relative : relativePos) {
			xpos = x + relative;
			if (y - 1 >= 0 && xpos >= 0 && xpos < bi.getWidth()
					&& bi.getRGB(xpos, y - 1) != Color.WHITE.getRGB()) {
				chosenCluster = clusters.getClusterid(xpos, y - 1);
				neighborClusters.add(chosenCluster);
			}
		}

		List<Integer> neighbors = new ArrayList<Integer>(neighborClusters);
		if (neighbors.size() > 0) {
			if (neighbors.size() > 1) {
				Collections.sort(neighbors);
				setAllFromDeprecatedClustersToMergedCluster(neighbors, clusters);
			}
			chosenCluster = neighbors.get(0);
		}

		return chosenCluster;
	}

	private static void setAllFromDeprecatedClustersToMergedCluster(
			List<Integer> neighbors, ClusterInfo clusters) {
		Integer newCluster = neighbors.get(0);

		Map<Integer, Set<Integer>> flagN;

		Integer oldCluster;
		for (int i = 1; i < neighbors.size(); i++) {
			oldCluster = neighbors.get(i);
			flagN = clusters.getClusterMap(oldCluster);
			for (Integer x : flagN.keySet()) {
				for (Integer y : flagN.get(x)) {
					clusters.addToClusterReferences(newCluster, x, y);
				}
			}
			clusters.remove(oldCluster);
		}
	}
}