# Tree

package trie;

import java.util.ArrayList;

/**
 * This class implements a Trie. 
 * 
 * @author Julian Calle
 *
 */
public class Trie {
	
	// prevent instantiation
	private Trie() { }
	
	/**
	 * Builds a trie by inserting all words in the input array, one at a time,
	 * in sequence FROM FIRST TO LAST. (The sequence is IMPORTANT!)
	 * The words in the input array are all lower case.
	 * 
	 * @param allWords Input array of words (lowercase) to be inserted.
	 * @return Root of trie with all words inserted from the input array
	 */
	public static TrieNode buildTrie(String[] allWords) {
		/** COMPLETE THIS METHOD **/
		
		// FOLLOWING LINE IS A PLACEHOLDER TO ENSURE COMPILATION
		// MODIFY IT AS NEEDED FOR YOUR IMPLEMENTATION
		TrieNode root = new TrieNode(null, null, null);
		if(allWords.length == 0) 
		{
			return root;
		}	   
		root.firstChild = new TrieNode(new Indexes(0,(short)(0),(short)(allWords[0].length() - 1)), null, null);
		TrieNode ptr = root.firstChild, bef = root.firstChild;
		int x = -1, start = -1, end = -1, wordIdx = -1;
		for(int i = 1; i < allWords.length; i++) 
		{
			String vari1 = allWords[i];
			while(ptr != null) {
				start = ptr.substr.startIndex;
				end = ptr.substr.endIndex;
				wordIdx = ptr.substr.wordIndex;
				if(start > vari1.length()) {
					bef = ptr;
					ptr = ptr.sibling;
					continue;
				} 
				x = similarUntil(allWords[wordIdx].substring(start, end+1),vari1.substring(start));	   
				if(x != -1) {
					x += start;
				}
				if(x == -1) {
					bef = ptr;
					ptr = ptr.sibling;
				}
				else {
					if(x == end) {
						bef = ptr;
						ptr = ptr.firstChild;
					}
					else if (x < end){
						bef = ptr;
						break;
					}
				}
			}
		   
			if(ptr == null) {
				Indexes alpha = new Indexes(i, (short)start, (short)(vari1.length()-1));
				bef.sibling = new TrieNode(alpha, null, null);
			} 
			else {
				Indexes currentIndexes = bef.substr;
				TrieNode currentFirstChild = bef.firstChild;
			   
				Indexes currWordNewIndexes = new Indexes(currentIndexes.wordIndex, (short)(x+1), currentIndexes.endIndex);
				currentIndexes.endIndex = (short)x;
			   
				bef.firstChild = new TrieNode(currWordNewIndexes, null, null);
				bef.firstChild.firstChild = currentFirstChild;
				bef.firstChild.sibling = new TrieNode(new Indexes((short)i,(short)(x+1),(short)(vari1.length()-1)),null, null);
			}
		   
			ptr = bef = root.firstChild;
			x = start = end = wordIdx = -1;
		}
	   
		return root;
	}
   
	private static int similarUntil(String a, String b){
		int w = 0;
		while(w<a.length() && w<b.length() && a.charAt(w)==b.charAt(w)){
			w++;
		}
		return (w-1);
	}
	
	/**
	 * Given a trie, returns the "completion list" for a prefix, i.e. all the leaf nodes in the 
	 * trie whose words start with this prefix. 
	 * For instance, if the trie had the words "bear", "bull", "stock", and "bell",
	 * the completion list for prefix "b" would be the leaf nodes that hold "bear", "bull", and "bell"; 
	 * for prefix "be", the completion would be the leaf nodes that hold "bear" and "bell", 
	 * and for prefix "bell", completion would be the leaf node that holds "bell". 
	 * (The last example shows that an input prefix can be an entire word.) 
	 * The order of returned leaf nodes DOES NOT MATTER. So, for prefix "be",
	 * the returned list of leaf nodes can be either hold [bear,bell] or [bell,bear].
	 *
	 * @param root Root of Trie that stores all words to search on for completion lists
	 * @param allWords Array of words that have been inserted into the trie
	 * @param prefix Prefix to be completed with words in trie
	 * @return List of all leaf nodes in trie that hold words that start with the prefix, 
	 * 			order of leaf nodes does not matter.
	 *         If there is no word in the tree that has this prefix, null is returned.
	 */
	public static ArrayList<TrieNode> completionList(TrieNode root,
										String[] allWords, String prefix) {
		/** COMPLETE THIS METHOD **/
		
		// FOLLOWING LINE IS A PLACEHOLDER TO ENSURE COMPILATION
		// MODIFY IT AS NEEDED FOR YOUR IMPLEMENTATION
		if(root==null){
			return null;
		}
		ArrayList<TrieNode> final1 = new ArrayList<TrieNode>();
		TrieNode ptr = root;
		while(ptr!=null)
		{
			if(ptr.substr==null)
			{
				ptr = ptr.firstChild;
			}	   
		String x = allWords[ptr.substr.wordIndex];
		String alpha = x.substring(0, ptr.substr.endIndex+1);
		if(x.startsWith(prefix) || prefix.startsWith(alpha))
		{
			if (ptr.firstChild!=null)
			{
				final1.addAll(completionList(ptr.firstChild,allWords,prefix));
				ptr=ptr.sibling;
			}
			else {
				final1.add(ptr);
				ptr=ptr.sibling;
			}
		}
		else 
		{
			ptr = ptr.sibling;
		}
		}
	   
		return final1;
	}
	
	public static void print(TrieNode root, String[] allWords) {
		System.out.println("\nTRIE\n");
		print(root, 1, allWords);
	}
	
	private static void print(TrieNode root, int indent, String[] words) {
		if (root == null) {
			return;
		}
		for (int i=0; i < indent-1; i++) {
			System.out.print("    ");
		}
		
		if (root.substr != null) {
			String pre = words[root.substr.wordIndex]
							.substring(0, root.substr.endIndex+1);
			System.out.println("      " + pre);
		}
		
		for (int i=0; i < indent-1; i++) {
			System.out.print("    ");
		}
		System.out.print(" ---");
		if (root.substr == null) {
			System.out.println("root");
		} else {
			System.out.println(root.substr);
		}
		
		for (TrieNode ptr=root.firstChild; ptr != null; ptr=ptr.sibling) {
			for (int i=0; i < indent-1; i++) {
				System.out.print("    ");
			}
			System.out.println("     |");
			print(ptr, indent+1, words);
		}
	}
 }
