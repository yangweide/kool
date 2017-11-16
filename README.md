# kool
ui图片中间显示
using UnityEngine;
using UnityEngine.UI;
using UnityEngine.EventSystems;
using System.Collections;
using System.Collections.Generic;
using System;
/// <summary>
/// UGUI ScrollRect 滑动元素居中
/// </summary>
public class UUICenterOnChild : MonoBehaviour, IBeginDragHandler, IEndDragHandler
{
    public float scrollSpeed = 8f;
    public Transform uUIGrid;
    private ScrollRect scrollRect;
    private float[] pageArray;
    private float targetPagePosition = 0f;
    private bool isDrag = false;
    private int pageCount;
    private int currentPage = 0;
    private List<Transform> items = new List<Transform>();
    // Use this for initialization
    void Awake()
    {
        scrollRect = GetComponent<ScrollRect>();
        InitPageArray();
    }
    void OnEnable()
    {
        InitPageArray();
    }
    void Start()
    {
        
    }
    void InitPageArray()
    {
        foreach (Transform item in uUIGrid)
        {
            items.Add(item);
        }
        pageCount = uUIGrid.childCount;
        pageArray = new float[pageCount];
        for (int i = 0; i < pageCount; i++)
        {
            pageArray[i] = (1f / (pageCount - 1)) * i;
        }
    }
 
    // Update is called once per frame
    void Update()
    {
        if (!isDrag)
        {
            scrollRect.horizontalNormalizedPosition = Mathf.Lerp(scrollRect.horizontalNormalizedPosition, targetPagePosition, scrollSpeed * Time.deltaTime);
        }
    }
 
    public void OnBeginDrag(PointerEventData eventData)
    {
        isDrag = true;
    }
 
    public void OnEndDrag(PointerEventData eventData)
    {
        isDrag = false;
        float posX = scrollRect.horizontalNormalizedPosition;
        int index = 0;
        float offset = Math.Abs(pageArray[index] - posX);
        for (int i = 1; i < pageArray.Length; i++)
        {
            float _offset = Math.Abs(pageArray[i] - posX);
            if (_offset < offset)
            {
                index = i;
                offset = _offset;
            }
        }
        targetPagePosition = pageArray[index];
        currentPage = index;
    }
    //想左移动一个元素
    public void ToLeft()
    {
        if (currentPage > 0)
        {
            currentPage = currentPage - 1;
            targetPagePosition = pageArray[currentPage];
        }
    }
    //想右移动一个元素
    public void ToRight()
    {
        if (currentPage < pageCount - 1)
        {
            currentPage = currentPage + 1;
            targetPagePosition = pageArray[currentPage];
        }
    }
 
    public int GetCurrentPageIndex()
    {
        return currentPage;
    }
    public void SetCurrentPageIndex(int index)
    {
        currentPage = index;
        targetPagePosition = pageArray[currentPage];
    }
    public int GetTotalPages()
    {
        return pageCount;
    }
}
